# Andrew Odeyemi | AWS Cloud Portfolio

Open index.html in a browser to preview the site. HTML contains the text and sections. CSS controls the layout and colors. No build tools are required.

## Put the files on GitHub

1. At https://github.com/new, create a repository named cloud-portfolio under sokajnr. Choose Public if you want employers to see the code. Initialize it with a README.
2. Unzip the project and use Add file → Upload files. Upload index.html, styles.css, and README.md at the root of the repository. Commit the changes.

## Publish on AWS

1. Sign in to your AWS account and open Amplify Hosting. Review AWS Amplify pricing before deploying.
2. Create a new app, select GitHub, and authorize access to just cloud-portfolio. Select the repository and its main branch.
3. Leave the build command empty and set the build output directory to / (the repository root). Save and deploy.
4. Open the generated HTTPS link in a private browser window. Check that the page styles load and both navigation links work.
5. Edit the deployment status and description in index.html after verifying deployment. Commit that edit on GitHub, then confirm Amplify publishes the change. Use the verified AWS URL on your résumé.

AWS walkthrough: https://builder.aws.com/content/3LOwx6ofpPdaX6dKye9ZLNdvXl7
AWS pricing: https://aws.amazon.com/amplify/pricing/
