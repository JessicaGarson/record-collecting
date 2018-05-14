# Solving the Problem of Buying the Same Record Many Times with MongoDB Stitch and GitHub Pages

![Records I Own](https://res.cloudinary.com/dkibchpur/image/upload/v1526325958/records%20I%20own.png)

I collect vinyl records but every so often I buy a record I already own. I created a site where I can see the records I already own. This tutorial will walk you through how to make this site.

MongoDB Stitch allows us to quickly create a database and connect to the code using JavaScript to query. GitHub Pages lets us host our site without much configuration.

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
<html>
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
First, you will need to sign up for an account with [MongoDB Atlas](https://www.mongodb.com/cloud/atlas). From there you will see a screen with a title that says "clusters". You will want to click to where it says "Build a New Cluster":

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

## Step 4: Adding Authentication to Our App
Now that we have our Stitch app set up, we can add in authentication that allows users to login. What is really cool about Stitch is that it is one click to set up security rules such as this one.

Let's turn on Authentication for the app:

![step 4](https://res.cloudinary.com/dkibchpur/image/upload/v1526312753/auth.png)

## Step 5: Adding the Database and Collection Names
We should go ahead and name our database and collection:

![step 5](https://res.cloudinary.com/dkibchpur/image/upload/v1526324065/Collection.png)

## Step 6: Changing the Rules
To allow our changes to be made to the database we'll need to click where it says "mongodb-atlas" in the side panel to make changes to the security rules of our clusters.

The filters should be changed so they the look like this picture:

![Step 6 Filters](https://res.cloudinary.com/dkibchpur/image/upload/v1526324675/filters.png)

The field rules should be changed so they match this picture:
![Step 6 Field Rules](https://res.cloudinary.com/dkibchpur/image/upload/v1526324782/field%20rules.png)

## Step 7: Updating The JavaScript and HTML
We'll need to update our JavaScript and HTML so that it connects to the database, submits new records and allows us to view our records. We also needed to add in a form so we can add new records to our database.

We can update our `index.html` file to the following:

```html
<html>
    <head>
        <script src="https://s3.amazonaws.com/stitch-sdks/js/library/v3/stable/stitch.min.js"></script>
        <script>
         const clientPromise = stitch.StitchClientFactory.create('app-name');

         let client;
         let db;
         function displayRecordsOnLoad() {
             clientPromise.then(stitchClient => {
                 client = stitchClient;
                 db = client.service('mongodb', 'mongodb-atlas').db('your-database-name');
                 return client.login().then(displayRecords)
             });
         }

         function displayRecords() {
             db.collection('records').find({}).limit(1000).execute().then(docs => {
                 var html = docs.map(c => "<div>" + c.record + "</div>").join("");
                 document.getElementById("records").innerHTML = html;
             });
         }

         function addRecords() {
             var foo = document.getElementById("new_record");
             db.collection("records").insertOne({owner_id : client.authedId(), record: foo.value}).then(displayRecords);
             foo.value = "";
         }
        </script>
        <title>Records I Own</title>
        <link href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
        <link href="https://getbootstrap.com/examples/jumbotron-narrow/jumbotron-narrow.css" rel="stylesheet">
    </head>
    <body onLoad="displayRecordsOnLoad()">
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
        Below are records that I own:
        <div id="records"></div>
        <hr>
        Add a Record: <input id="new_record"><input type="submit" onClick="addRecords()">

    </body>
</html>
```
You will need to make sure your database and app-name are updated to your own.

## Step 8 - Adding to GitHub Pages
To add our code to GitHub Pages deploy we'll need to first commit our changes to GitHub. In our command line we'll need to commit our file we changed:

```bash
git add index.html
git commit -m "adding index.html to GitHub"
git push
```

From here we can go to our repository in the browser of GitHub and go to the settings section. In the section entitled "GitHub Pages" select the source as master branch.

![step 8](http://res.cloudinary.com/dkibchpur/image/upload/v1526325857/gh%20pages.png)

Once this is published it will give you the site this has been published to.

## Conclusion
A good next step would be adding an integration with a webhook such as Twilio to the site. The final product can be found at [jessicagarson.com/record-collecting](http://jessicagarson.com/record-collecting/).
