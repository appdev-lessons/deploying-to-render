# Deploying a web site to Render

Congratulations! You've built a web application using Ruby, and now it's time to share it with the world. In this guide, we'll walk you through the process of deploying your Ruby app on the internet using [Render](https://render.com). Render is great, because we can use if for not only static web sites, like our “Hello, World” and “Link in bio” projects, but also dynamic (i.e. Ruby backend) web sites that we’ll build with the Sinatra and Rails frameworks.

Use your GitHub account to [sign-up](https://dashboard.render.com/register), and let’s see how we can serve our app.

## Render Offering and Payment

Render offers a free usage tier known as the "[Individual Plan](https://render.com/pricing)" that is perfect for hosting side projects and learning purposes. This plan includes 750 hours of running time per month, 100 GB of egress bandwidth, and 500 build minutes. The best part is, no credit card is required for the free tier.

<aside markdown="1">
Please note that databases on the free plan are deleted after 90 days. If you plan to use your app beyond 90 days, consider upgrading to a paid plan.
</aside>

Web Services on the free instance type are automatically spun down after 15 minutes of inactivity. When a new request for a free service comes in, Render spins it up again so it can process the request.

This will cause a delay in the response of the first request after a period of inactivity while the instance spins up.

If you decide to host larger apps, and want something more powerful to serve real customers, then you can easily scale Render up to a paid plan. Alternatively, you may end up going with a service like Heroku, Fly or hatchbox.io. But, the Render individual plan should be sufficient (and free!) for now.

## Aside: How does Render work?

Render supports direct deployment of Ruby applications without the need for containers (e.g. Fly.io). You can deploy your Ruby app by connecting it to your Git repository. Whenever you merge into your `main` branch, Render copies all of your code to a server and runs the app similar to how you might in a codespace.

Render makes deploying Ruby applications straightforward by supporting direct deployment without the need for containers (e.g., Fly.io). The deployment process is based on an ["Infrastructure as Code"](https://render.com/docs/infrastructure-as-code) framework, where you define a `render.yaml` file to declare all the services required to deploy your app.

In simple terms, whenever you make changes to your `main` branch, Render re-deploys your app automatically.

Now, let's dive into deploying your app!

## Deploying your app

### Infrastructure as Code (IAC)

Before we deploy, let's set up the `render.yaml` file in your project repository. This file tells Render how to manage your app's deployment.

#### `render.yaml`` file

Create a new file named `render.yaml` in your project root directory and replace `MYAPPNAME` with your application's name (e.g. `hello-world` or something else; only use letters and dashes in the name, no spaces).

```yaml
services:
  - type: web
    name: MYAPPNAME # the name of this service, eg your app name
    env: ruby # this app is written in ruby
    plan: free # make sure to set this to free or you'll get billed $$$
    buildCommand: "./bin/render-build.sh"
    startCommand: "./bin/render-start.sh"
```

<aside markdown="1">
For more complex applications, you might define additional resources like databases or Redis servers that need to be deployed together. Checkout [this render.yaml file](https://render.com/docs/deploy-rails#use-renderyaml-to-deploy) for a Ruby on Rails app with a Postgresql database.
</aside>


#### Build scripts

Now, we need to create two files referenced in `render.yaml`: `bin/render-build.sh` and `bin/render-start.sh`. Create those two files in your repository. Copy and paste this contents into those files.

`bin/render-build.sh`:

```
#!/usr/bin/env bash
# exit on error
set -o errexit

bundle install

# For Ruby on Rails apps uncomment these lines to precompile assets and migrate your database.
# bundle exec rake assets:precompile
# bundle exec rake assets:clean
# bundle exec rake db:migrate
```

`bin/render-start.sh`:

```
#!/usr/bin/env bash
# exit on error
set -o errexit

# Uncomment the line depending on the framework you are deploying

# Static HTML
# bundle exec rackup

# Sinatra
# bundle exec ruby app.rb

# Ruby on Rails
# bundle exec puma -C config/puma.rb
```

These scripts instruct the server to install the required gems and run your application.

Commit and push these changes to your repository to proceed.

### Create a new Blueprint

For every app you deploy on Render, you'll need to create a new "Blueprint Instance" to specify rules for that app.

<aside markdown="1">
Detailed instructions on creating Blueprints can be found in the [Render Docs](https://render.com/docs/deploy-rails#deploy-to-render).
</aside>

First, go to your Render [dashboard](https://dashboard.render.com/), and click on "Blueprints" and then "New Blueprint Instance." (Note: you can also click the "New +" button on the top and select "Blueprint".)

![](/assets/render-new-blueprint-1.png)
{: .bleed-full }

Make sure to connect Render to your GitHub account and select the repository you want to deploy. If you don't see the repository you want to deploy, click "configure account" to allow Render access to the repository you want to deploy. It's a good idea to only allow access to the repositories you'd like to deploy. After you follow the steps below, you will need to wait a few minutes while your app builds and deploys.

![](/assets/render-new-blueprint-2.png)
{: .bleed-full }

---

![](/assets/render-new-blueprint-3.png)

---

![](/assets/render-new-blueprint-4.png)

<div class="bg-blue-100 py-1 px-5" markdown="1">

For subsequent apps that you create, you can click the "configure account" button on your Render dashboard and add more repositories by following similar steps. However, this time, you will be brought to your GitHub account settings where you will see a similar interface to add more repositories and save the changes.
</div>

![](/assets/render-new-blueprint-5-fixed.png)
{: .bleed-full }

### Deploy your Blueprint

Now that your GitHub repository is connected, name your Blueprint, and choose the branch to deploy from (likely you should just leave this as `main`), then enter a name for the app and click "Apply":

![](/assets/render-new-blueprint-6-fixed.png)
{: .bleed-full }

<div class="bg-red-100 py-1 px-5" markdown="1">

If the Blueprint interface does not find your `render.yaml` file, it may mean that you need to [commit and push](https://learn.firstdraft.com/lessons/50-git-commit-and-push) your updated `render.yaml` file to GitHub. Do so and click "Retry", then enter a name for the Blueprint and click "Apply":

![](/assets/render-new-blueprint-7.png)
</div>

Render will detect the `render.yaml` file and start deploying your application. 🪄

When the deployment is complete, click on your new web service.

---

![](/assets/render-new-blueprint-8.png)

---

In your web service, you can monitor deployments, logs, environment variables, and more. Most importantly, you'll get a live URL to visit your app.

![](/assets/render-new-blueprint-9.png)
{: .bleed-full }

<div class="bg-blue-100 py-1 px-5" markdown="1">

Be patient while you wait for the app to finish deploying and the link to go live. This can take a few minutes!

Here's something neat: Because we set Render to deploy from our `main` branch and connected the app with the GitHub repository, anytime you make a change to your app and commit and push that change, the live app will re-deploy with your updates!
</div>

And that's it! Your web site is live, and you can share the link with anyone. 🎉

Your [Render dashboard at dashboard.render.com](https://dashboard.render.com/) is where you can view and interact with any of your deployed apps.

If you want a custom domain name for your app, you can follow the steps outlined in [this manual from Render](https://render.com/docs/custom-domains).

You've successfully deployed your Ruby app on Render! Now your creation is accessible to the world. Feel free to experiment, expand your app, and keep learning.

---
