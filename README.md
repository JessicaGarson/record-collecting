# Solving the Problem of Buying the Same Record Many Times with MongoDB Stitch, Twilio and GitHub Pages

I collect vinyl records but every so often I buy a record I already own. I created a site where I can see the records I already own. To update the records that I own I wanted to be able to text each new record I built to a number. This tutorial will walk you through how to make this site.

MongoDB Stitch allows us to quickly create a database and connect to the code using JavaScript to query. We can also connect via a webhook to Twilio, so that we can update our site by sending text messages to a number. GitHub Pages lets us host our site without much configuration.

## Step 1 - Create a GitHub Repo
In your GitHub account you will need to create a repository for this project called `record-collecting`.

We will also want to clone this repository into our local environment with the following in our command line:

```bash
git clone https://github.com/your-github-username/record-collecting.git
```

Be sure to replace your-github-username with your own username. If you named your repository something different be change record-collecting to the new name.

## Step 2 - Building the Site
To build the site let's start with some very basic HTML to create a very simple static site.

Create a file called `index.html` inside of your `record-collecting` directory and populate it with the following code:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>Records I Own</title>
    <link href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
    <link href="https://getbootstrap.com/examples/jumbotron-narrow/jumbotron-narrow.css" rel="stylesheet">
  </head>
  <body>
    <div class="container">
      <div class="header clearfix">
        <nav>
          <ul class="nav nav-pills pull-right">
            <li role="presentation"><a href="http://jessicagarson.com" target="_blank">Built by Jessica Garson</a></li>
            <li role="presentation"><a href="https://github.com/JessicaGarson/record-collecting/blob/master/README.md" target="_blank">Tutorial</a></li>
          </ul>
        </nav>
      </div>
      <div class="jumbotron">
        <h1>Records I Own</h1>
      </div>
  </body>
</html>
```
If you view your file in the browser it should look like this:

![step 1 image](https://res.cloudinary.com/dkibchpur/image/upload/v1526309323/Records.png)

## Step 3 - Stetting Up MongoDB Atlas Cluster
First, you will need to sign up for an account with [MongoDB Atlas](https://www.mongodb.com/cloud/atlas). From there you will see a screen with a title that says clusters. You will want to click to where it says "Build a New Cluster":

![step 2 cluster](https://res.cloudinary.com/dkibchpur/image/upload/v1526309076/cluster.png)

From there make sure you select the cloud provider and region. I selected the first recommend region and the AWS free tier:

![step 2 region](https://res.cloudinary.com/dkibchpur/image/upload/v1526309513/cloud%20and%20region.png)

I set the cluster tier as M0:
![step 2 tier](https://res.cloudinary.com/dkibchpur/image/upload/v1526310472/Cluster%20Tier.png)

I also renamed the cluster to make things easier to work with:

![step 2 rename](http://res.cloudinary.com/dkibchpur/image/upload/v1526310163/Rename%20Cluster.png)

At the bottom of the page click "Create Cluster" to make your cluster, it could take 7-10 minutes before this gets fully set up:

![step 2 create cluster](https://res.cloudinary.com/dkibchpur/image/upload/v1526311045/create%20cluster.png)

## Step 4 - Create a Stitch App
Now that we have an Atlas cluster, we can create a Stitch App.

On the left side panel click where it says "Stitch Apps" and from there click where it says "Create New Application":

![step 3 create app](https://res.cloudinary.com/dkibchpur/image/upload/v1526311722/create-stitch.png)

From there you will be asked to name your app and it will take about 1-3 minutes to full spin up your new app:

![step 3 create app name](https://res.cloudinary.com/dkibchpur/image/upload/v1526312016/create-app-name.png)

## Step 4:
