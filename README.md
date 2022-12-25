# Hosting a static website with HTTPS in a private S3 bucket.

It is very convenient to use an S3 bucket as a hosting space for a static website, but people usually get confronted with the fact that TLS encryption can not be provided to the website as S3 does not have that facility..

Although S3 is a reliable hosting space, the lack of SSL support makes it less attractive to use.

In this article, I’ll try to explain how to use S3 as a hosting space with TLS without making the bucket “Public.” We deploy Amazon’s Content Delivery Network (CDN) offering AWS CloudFront to do this.

![](https://miro.medium.com/max/1400/1*hiCLmoWUw5_TSSAJu6L1KQ.png)

This article will teach you how to:

-   Create an S3 bucket and configure it to host static websites.
-   Make a Route 53 record set.
-   Create a CloudFront distribution and connect it to a custom domain.
-   SSL and AWS Certificate Manager are used to secure the connection.

In an S3 bucket, we will host a static demo website. Secondly, in collaboration with Route 53 DNS, we will create a custom domain. Then, we’ll launch a CloudFront CDN, connect it to our custom domain via Route 53, and encrypt the entire connection with an SSL certificate and AWS Certificate Manager.

**Prerequisites.**

-   You must have your static website, which should be tested.
-   You must have an AWS account access.
-   Access to Amazon services : Amazon S3, Amazon Cloudfront, Amazon Certificate Manager (ACM)
-   A valid domain incorporated as a hosted zone in Route 53 and DNS management access.

**Steps involved in this activity.**

1.  Create an S3 bucket without any public access.
2.  Request an SSL certificate in Amazon Certificate Manager(ACM) for the custom domain.
3.  Deploy CloudFront CDN and upload SSL/TLS certificate.
4.  Link CloudFront CDN, S3, custom domain and SSL certificate via Route 53.

**Step 1: Create an S3 bucket without any public access.**

Go to the Amazon Web Service Console at [https://aws.amazon.com/console/](https://aws.amazon.com/console/), then choose : Sign In to the Console button in the upper right corner.

After successfully logging in, navigate to the Services menu.  
Amazon has a plethora of services, so simply enter the name of the service you’re looking for in the search box, in our case S3.

![](https://miro.medium.com/max/1400/1*87mhE0aeUfx7gzsPbc2bkw.png)

Open the S3 Management console after logging into AWS and clicking Create Bucket.

![](https://miro.medium.com/max/1400/1*2fUQqOvyTlU66Ndg2C8tRQ.png)

Give your bucket the same name as your domain.  
Because my domain name is “pratheeshsatheeshkumar.tech,” I used “shopping.pratheeshsatheeshkumar.tech” as the bucket name.

> Note : Bucket name should be unique in the entire AWS s3 namespace.

You have to take care to keep this bucket as private, by blocking the Public Access for this bucket.

![](https://miro.medium.com/max/1400/1*gWuza8BmMpg3y4_INA7cdw.png)

So, you have to keep the “Block all public access” button enabled.

![](https://miro.medium.com/max/1400/1*Al4moNQgNUS9KThqb6zgxg.png)

Keep all other settings including Object ownership in the recommended condition.

![](https://miro.medium.com/max/1400/1*e0BKLbQsbDSlm11rJ1-tRQ.png)

Finally, press the **Create bucket** button.

Now your S3 bucket will be created.

![](https://miro.medium.com/max/1400/1*yu9PPjRnvuIPV_tfQhZi3Q.png)

Now, You have to edit the bucket properties to disable Static Website Hosting.

![](https://miro.medium.com/max/1400/1*8uMz6kh9F2kjXo5x33wsog.png)

It is time to upload required static website files to “shopping.pratheeshsatheeskumar.tech” bucket.

![](https://miro.medium.com/max/1400/1*UPBMvuDo0FJQELfKqBb2QQ.png)

All the files are successfully uploaded.

![](https://miro.medium.com/max/1400/1*RN6atdb5a0eyVbKt2tVWXg.png)

**Step 2: Request an SSL certificate in Amazon Certificate Manager(ACM) for the custom domain.**

![](https://miro.medium.com/max/1400/1*m2QRM4lNhqBtat1oqwRHxQ.png)

![](https://miro.medium.com/max/1400/1*x_6-AqyRdMlU13h-r7klzA.png)

choose **Request a public certificate** option and click next.

> It’s crucial that you choose the AWS North Virginia region (US-East-1).  
> Only this location is recognised by CloudFront for it’s ACM certificates.

**Request Public Certificate.**

pratheeshsatheeshkumar.tech is a domain that is not a subdomain of *.pratheeshsatheeshkumar.tech

(for example, [https://www.pratheeshsatheeshkumar.tech](https://www.pratheeshsatheeshkumar.tech) is a subdomain).  
In case, you want your cert to handle both the main domain and subdomain, for example, [https://pratheeshsatheeshkumar.tech](https://pratheeshsatheeshkumar.tech) and [https://www.pratheeshsatheeshkumar.tech](https://www.pratheeshsatheeshkumar.tech) or [https://shopping.pratheeshsatheeshkumar.tech](https://shopping.pratheeshsatheeshkumar.tech)

Then, you must add two entries for pratheeshsatheeshkumar.tech and *.pratheeshsatheeshkumar.tech. You must verify that you own the domain entered above in order for Amazon to issue you an SSL certificate.

![](https://miro.medium.com/max/1400/1*lqmWRla12qO6irnFEHG4HQ.png)

**Domain name Validation method:**

DNS Validation is the most flexible and quick method if your domain name is available in the Route 53 as a hosted zone.

![](https://miro.medium.com/max/1400/1*dXFq4QVyJjA5-bOhbce6Qw.png)

In this case you can simply press **Create records in Route 53** to do validation.

![](https://miro.medium.com/max/1400/1*K63ReebBukiNi03g_ZPlTQ.png)

Once you verify the domain name, certificate will be immediately issued and will be available for use.

**Step 3: Deploy CloudFront CDN and upload SSL/TLS certificate.**

![](https://miro.medium.com/max/1400/1*u0Y9Lu5TocZDsvMVpEP36Q.png)

Press “**Create distribution**” button. Now, you have to select the domain name of the S3 bucket as the origin of the distribution.

![](https://miro.medium.com/max/1400/1*dw-_BBz8T4BhYgt7J66Pkg.png)

**Creation of Bucket policy for CloudFront access.**

In the distribution menu you have to specify the origin access as **Legacy access identifiers.** There you have to create an **Origin Access Identity (OAI)** for the S3 bucket.

There is a feature of creation and auto-update of Bucket Policy, which you can enable. This policy we can verify in the S3 bucket permissions.

![](https://miro.medium.com/max/1400/1*c1hLHHbRHM1XEIvmjyjDtg.png)

You can view the JSON containing the bucket policy if you check S3 bucket now. It is very clear from the policy that s3:GetObject access is provided to the CloudFront via OAI. You have to specify the root object in the bucket, which is i**ndex.html** in my case.

![](https://miro.medium.com/max/1400/1*tyKylN_xxWEag3kaXV7PHQ.png)

It is very important to add the exact domain name we want to use for the website as a CNAME in this distribution. You have to add the certificate requested for *.pratheeshsatheeshkumar.tech in the step 2. As I have said earlier, this certificate has to be in us-east-1 region. The objective of this exercise is to enable https access from the client browser to the cloud front domain name.

![](https://miro.medium.com/max/1400/1*u69flNpOpC7E7sm874wpXQ.png)

You can enable the **Redirect HTTP to HTTPS viewer protocol policy** to achieve SSL/TLS encryption enforcement.

![](https://miro.medium.com/max/1400/1*oZEVpZWCqM25V6I8QuF36A.png)

![](https://miro.medium.com/max/1400/1*KpgHlYFdsV9HH_KoxMG0lA.png)

Press **Create Distribution** button and it will be enabled quickly. But, it will take up to 30 minutes to get it deployed.

![](https://miro.medium.com/max/1400/1*-Yqm3IifCRaPiaxP0O3nxA.png)

**Step 4 : Setup records in Route 53 Hosted Zone for the custom domain.**

As a prerequisite, we should have a domain in a hosted zone where we need privileges to create records.

A domain in a hosted zone consists of NS records and State Of Authority (SOA) record. the CNAME in the hosted zone is created by the ACM while validating the domain name through DNS validation.

![](https://miro.medium.com/max/1400/1*x0HI_SDqJl2iXOHOnJbszQ.png)

Now , you have to add a subdomain A-record of shopping.pratheeshsatheeshkumar.tech, which should be aliased to the end point name of our CloudFront distribution(available in a drop down list).

![](https://miro.medium.com/max/1400/1*JYzv8q6i17jDkoNdttpjmw.png)

**Testing of the demo website**

Accessing the URL [https://shopping.pratheeshsatheeshkumar.tech](https://shopping.pratheeshsatheeshkumar.tech) has successfully loaded the website hosted in the S3 bucket as show in the image. You can see a successful http to https conversion if you try to access [http://shopping.pratheeshsatheeshkumar.tech](https://shopping.pratheeshsatheeshkumar.tech).

![](https://miro.medium.com/max/1400/1*9GS29dVlIWJ6_APdUAHl5g.png)

**Conclusion**

It is relatively simple to deploy a static website with S3 with the aid of Amazon Cloudfront and Amazon ACM, along with more advanced domain and SSL certificates.  
Without having to worry about the server infrastructure, Amazon S3 is incredibly affordable and simple to set up. In the end, you save time and resources so that your teams can concentrate more on creating fantastic features for your apps.

S3 is great for static content, but it’s not the best option if your pages contain dynamic content or analytics data.  
In my use cases, I utilize S3 for things like product landing pages and documentation for APIs.

[Pratheesh Satheesh Kumar](https://www.linkedin.com/in/pratheesh-satheesh-kumar/)

**Read my article in medium.com.**

 <a target="_blank" href="https://github-readme-medium-recent-article.vercel.app/medium/@yespratheesh/0"><img src="https://github-readme-medium-recent-article.vercel.app/medium/@yespratheesh/0" alt="Recent Article 0">
 <a target="_blank" href="https://github-readme-medium-recent-article.vercel.app/medium/@yespratheesh/1"><img src="https://github-readme-medium-recent-article.vercel.app/medium/@yespratheesh/1" alt="Recent Article 1">
