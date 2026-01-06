# Preprint Recommender Runner

This repository is linked to [`preprint-recommender`](https://github.com/adigioacchino/preprint-recommender) and is used to run the preprint recommender system using GitHub Actions.

To set up the GitHub Actions workflow, follow the steps below.

## Repository setup

1. Create a duplicate of this repository using the "Use this template" button (more info in the official [GitHub documentation](https://docs.github.com/en/repositories/creating-and-managing-repositories/creating-a-repository-from-a-template)).
2. Go to the "Settings" tab of your new repository.
3. Select "Secrets and variables" > "Actions".
4. Click on "New repository secret".
5. Add a new secret with the name `GENAI_API_KEY` and set its value to your Google GenAI API key.

## Seed papers setup

1. Create a folder named `seed_papers` in the root of your forked repository.
2. Add JSON files representing your seed papers in this folder. Each JSON file should have the following structure:

```json
{
  "title": "Title of the seed paper",
  "abstract": "Abstract of the seed paper"
}
```

Do not forget to push the folder with your seed papers to your repository, as the GitHub Actions workflow will need to access them.

We recommend starting with about 10 seed papers that are closely related to your research interests.
Adding too few seed papers may result in less relevant recommendations, while adding too many results in higher number of suggestions, which reduces the interest of the daily recommendations.

### Tips for selecting seed papers

A good set of seed papers is crucial for obtaining relevant recommendations.
The objective is to balance specificity and diversity in the topics covered by the seed papers, so to have recommendations that are both relevant and varied.

Here are some tips for selecting effective seed papers:

- **Avoid Reviews**: Review articles are great, but often they cover a specific topic in a very broad way. This means they generally have broad abstracts, which in turns can result in tens of recommendations per day, most of which may not be relevant. Prefer research articles that focus on specific contributions. Use reviews only if they are very focused on a narrow topic.
- **Cover each topic**: If your research interests span multiple topics, ensure that you include at least a seed paper for each of these topics.
- **Avoid very similar papers**: Including multiple seed papers that are very similar to each other (such as v1 and v2 of a tool, or papers from the same research group on the same topic) is not useful. If you suggest many similar seed papers, this will also influence the threshold for recommendations (which is based on similarity to the seed papers), which might become too strict and likely exclude relevant recommendations that are not similar enough to the seed papers.
- **Trial and error**: Try it out! You can always modify the seed papers later, and in general it takes a few iterations to find the best set of seed papers for your needs.

## Configuration setup

As a last step, we need to configure `preprint-recommender` to decide which preprint servers we are interested in, and which categories to monitor.

To do that, you need to create a configuration file named `preprint-recommender-config.json` in the root of your forked repository.
This file should have the following structure:

```json
{
  "seedFolder": "./seed_papers",
  "arxivCategories": ["cs.AI", "cs.LG"],
  "biorxivCategories": ["bioinformatics", "genomics"],
}
```

You can customize the `arxivCategories` and `biorxivCategories` fields to match your research interests.
By removing one of the two fields, you can choose to monitor only one preprint server.

Once more, do not forget to push the configuration file to your repository.

### Tips for selecting categories

The reason why we introduce category-based filtering is that the overall number of papers uploaded daily on preprint servers can be very large and easily fill the daily quota of the Google GenAI API key.
By restricting the search to specific categories, we can reduce the number of candidate preprints to evaluate, while still obtaining relevant recommendations.

A good starting point to choose categories is to use your seed papers!
Look at the categories of those among them that are on arXiv and bioRxiv, and make sure to include those categories in your configuration file.
For the papers that are not on these preprint servers, try to find the closest matching categories.

If you want to receive more recommendations, you can always add more categories later: again, it is a trial-and-error process.

## (Optional) Email notifications setup

If you want to receive email notifications with the daily recommendations, you need to set up an additional secret in your repository, which are used by the GitHub Actions workflow to send emails via SMTP.
In the following steps, we will use a Gmail account as an example. Other email providers should work similarly, for more information check [this GitHub action](https://github.com/dawidd6/action-send-mail).

To use Gmail, you need first to enable 2-Step Verification in your Google account (if not already done) and then create an [App Password](https://support.google.com/accounts/answer/185833?hl=en).
Then, follow these steps:

1. Go to the "Settings" tab of your forked repository.
2. Select "Secrets and variables" > "Actions".
3. Click on "New repository secret".
4. Add a new secret with the name `EMAIL_USERNAME` and set its value to your Gmail address.
5. Click on "New repository secret" again.
6. Add a new secret with the name `EMAIL_PASSWORD` and set its value to the App Password you created earlier.

With this configuration, you will receive an email (from your own Gmail address) every time the workflow runs, containing the daily recommendations.

## Running the workflow

The GitHub Actions workflow is set to run daily at 3 AM UTC by default.
You can modify the schedule by editing the `scheduled-run.yml` file located in the `.github/workflows` folder of your repository.

To manually trigger the workflow, go to the "Actions" tab of your repository, select the "Scheduled Run" workflow from the left sidebar, and click on the "Run workflow" button.
Keep in mind that if you use a free Google GenAI API key, you will have a limited number of requests per day (1000 as of the time of writing), so running manually the script might quickly exhaust your quota, or let the subsequent scheduled runs fail due to lack of quota.
