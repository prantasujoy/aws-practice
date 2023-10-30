
# S3 static webhosting (coludfornt+R53+CI/CD)

## Description
An static website hosted on S3,distributed CDN using cloudfront,R53 for custom domain name and CI/CD from github 

## Services used
    1.S3
    2.Cloudfront
    3.R53
    4.ACM
    5.Aws Codebuild

## Architecture

![Alt architecture](https://github.com/prantasujoy/aws-practice/blob/main/s3_static_hosting/s3_static_svg.jpg?raw=true)


## step 1: Creating React App
    1.create a react app using create-react-app or vite
    2.Make changes in the app as needed
    3.run npm run build to build the react app,it will create the build directory

## step 2:Create S3 Bucket
    1.create a s3 bucket.(bucket name should match the website name we will use later)
    2.enable public access,add a [bucket policy]() to make it public 
    3.use bucket name something like this (<bucketname>.<domain name>)
    4.upload files: all files from the build directory (except  static folder)
    5.upload folder: upload static folder from build directory
    6.Later we will use CI/CD for this
    7.From the bucket properties: enable static web hosting,use host a static website option
    8.set index document to default(index.html) 
    9.Set protocol to http ( change to https later)
    10.open the url from static web hoisting, it will be up

## step 3:Registering Domain
    • Register a domain in AWS or third party domain registrar
    • Go to R53 and create hosted zone
    • enter domain name,and select public for type
    • click create hosted zone
    • it will crete 4 name server
    • for third party domain registrar,replace their name server with asws generated anme server
    • go to create record=>create a simple record
    • record name=>enter www
    • record type=>A
    • value/route traffic to =>Alias to s3 website,desired region, bucket name
    • If we go to the website name,it will show the website hosted in s3
    
## step 4: Creating Certificate using ACM:
    • Go to certificate manager
    • create a public certificate
    • domain name=>website name created previously
    • validation=>DNS
    • step 5: validation:create record in r53
    • it will create record in r53 hosted zone to prove the ownership
    
## step 5:Setting up Cloudfront distribution:
    • create distribution:
    • origin domain name:bucket name
    • viewer protocol policy:Redirect HTTP to HTTPS
    • Distribution setting=>Altername domain name=>website name that we created
    • Distribution setting=>ssl certificate=>custom ssl certificate=>select certificate created before
    • in the s3 bucket=>properties=>static web hosting=>protocol=>https
    • Go To r53 hosted zone=>domain name=>edit record to point to the cloudfront distribution instead of S3 bucket.
    • go to the website, now it will serve using cloudfront and website it secured now, as it is creating certificate
## step 6:CI/CD from github:
    • create a github repo 
    • add a new buildspec.yml file in the root directory of the app (buildspec.yml link)
    • change the values in the  buildspec.yml file as needed
    • push the code to the github repo

## step 7:Setting up code build:
    • codebuild=>create new project
    • source =>github (connect to github if first time, may take some time)
    • select the repo created
    • source version=>branch name for the react app
    • webhook=>check rebuild every time
    • event type=>push
    • operating syatem=>ubuntu
    • runtime=>standard
    • let it create a new service role
    • build details =>service role =>edit policy =>policy url
    • empty the bucket, puh updated code to github, it will populate the s3 bucket(with build directory)
    • go to codebuild, if every thing is ok, it will show success 
    






