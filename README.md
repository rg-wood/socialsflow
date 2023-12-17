
Crossposting social media workflow repository powered by [Actionsflow](https://github.com/actionsflow/actionsflow).

# 🏁 Getting Started

Building an Socialflows workflow is a five-step process:

1. **Create a public Github repository by this [link](https://github.com/socialflows/socialflows/generate).**

   Your Socialflows repository structure looks like this:

   ```sh
   ├── .github
   │   └── workflows
   │       └── actionsflow.yml
   ├── .gitignore
   ├── README.md
   └── workflows
   │   └── on-post.yml
   └── package.json
   ```

2. **Uncomment [`.github/workflows/actionsflow.yml`](/.github/workflows/actionsflow.yml) schedule event and trigger**

    ```yml
    on:
      schedule:
        - cron: "*/15 * * * *"
    ```
    > Note: To prevent abuse, by default, the schedule is commented, please modify the schedule time according to your own needs, the default is once every 15 minutes. Learn more about schedule event, please see [here](https://docs.github.com/en/actions/reference/events-that-trigger-workflows#schedule)


    ```yml
    on:
      activitypub:
        host: toot.io
        user: <toot-user>
    ```

3. **Uncomment the jobs for your socials**

   Configure the social media account that you'll use as the source of your posts by uncommenting them in `on-post.yml`. For example, we've enabled the send tweet workflow here: 

   ```yaml
   on:
     activitypub:
       host: toot.io
       user: testgrislyeye
   
   jobs:
     print:
       name: Print
       runs-on: ubuntu-latest
       steps:
         - name: 🏁 Running Socialflows
           env:
             uri: ${{on.activitypub.outputs.uri}}
             message: ${{on.activitypub.outputs.message}}
             replyTo: ${{on.activitypub.outputs.replyTo}}
           run: echo 🔫 Socialflows triggered by $uri
   
   jobs:
     twitter:
       runs-on: ubuntu-latest
       
       ...
   
         - uses: rg-wood/send-tweet-action@v0.16
           with:
             status: ${{ on.activitypub.outputs.message }}
             replyto: ${{ steps.load-history.outputs.status }}
             consumer-key: ${{ secrets.TWITTER_CONSUMER_API_KEY }}
             consumer-secret: ${{ secrets.TWITTER_CONSUMER_API_SECRET }}
             access-token: ${{ secrets.TWITTER_ACCESS_TOKEN }}
             access-token-secret: ${{ secrets.TWITTER_ACCESS_TOKEN_SECRET }}
   
         ...
   ```
   
   Comment out the social media jobs you want to crosspost to.
   
4. **Configure you credentials**

Setup the credential secrets for you social media accounts:

##### 🐦 Twitter

You need to set-up consumer and access tokens as GitHub Action secrets in your workflow project. See the [send-tweet-action documentation](https://github.com/marketplace/actions/send-and-reply-tweet-action#secret-configuration) for full instructions.

##### ☁️ BlueSky

You need to configure the authority and login credentials for your Bluesky account as GitHub Action. See the [send-bluesky-post documentation](https://github.com/marketplace/actions/send-and-reply-bluesky-action#specify-authority) for full instructions.


5. **Commit and push your updates to Github**

Then, Actionsflow will run your workflows as you defined, you can view logs at your repository actions tab at [Github](https://github.com)

For more information, see [Full documentation](https://actionsflow.github.io/docs/)

## Run Locally

You can run self-hosted Actionsflow manually or [by docker](https://actionsflow.github.io/docs/self-hosted/#docker): 

## Prerequisites

1. Install [docker](https://docs.docker.com/get-docker/)
1. Install [act](https://github.com/nektos/act)
1. Install dependencies by running `npm install`

### Start

Start Actionsflow locally:

```bash
npm run start
# Then, the cron job and webhook server will start running
# The webhook endpoint will be ran at http://localhost:3000/webhook/
```

### Build

```bash
npm run build
# Then, the standard workflow files will be built at ./dist/workflows
```

### Clean

Actionsflow build will use cache for deduplicating the data, if you want to test your workflow with the same data, you may need to clean the cache by the following command:

```bash
# Clean the cache and dist folder.
npm run clean
```
