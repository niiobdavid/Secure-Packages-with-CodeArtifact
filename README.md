<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Secure Packages with CodeArtifact

**Project Link:** [View Project](http://learn.nextwork.org/projects/aws-devops-codeartifact-updated)

**Author:** Nii OB  
**Email:** davidniiamui@gmail.com

---

![Image](http://learn.nextwork.org/genuine_navy_mysterious_monkey/uploads/aws-devops-codeartifact-updated_1d79e699)

---

## Introducing Today's Project!

In this project, I will demonstrate how to set up CodeArtifact
as my CI/CD pipeline's artifact repository! 
I'm doing this to learn the importance and value of having an artifact repository. It's a huge time saver :)

### Key tools and concepts

Services I used were CodeArtifact, IAM, Amazon EC2, GitHub, and VSCode.
Key concepts I learnt include using artifact repositories, connecting Maven with Codeartifact, setting up IAM permissions to permit my EC2 instance to access CodeArtifact

### Project reflection

This project took about 2.5 hours, including documentation, troubleshooting, and a secret mission. The challenging part was handling CLI commands and fixing permission errors. 
The most rewarding moment was seeing CodeArtifact fill up with packages once the connection was properly set up.

This project is part three of a series of DevOps projects where I'm building a CI/CD pipeline! I'll be working on the next project tomorrow.

---

## CodeArtifact Repository

CodeArtifact is an artifact repository service, which means it can be used to create repositories to store a web app's packages and dependencies.
Engineering teams use artifact repositories for security, control, and reliability.

A domain is like a folder that holds together multiple repositories under the same project or the same organization. 
They help set up permissions for multiple CodeArtifact repositories in one go.
My domain is called nextwork.

A CodeArtifact repository can have an upstream repository, which means a public source of packages that Maven can visit if they can't find it in our local CodeArtifact repository.
My repository's upstream repository is Maven Central Repository, which is the largest Java repository (and is extremely helpful when building a Java web app).

![Image](http://learn.nextwork.org/genuine_navy_mysterious_monkey/uploads/aws-devops-codeartifact-updated_n4o5p6q7)

---

## CodeArtifact Security

### Issue

To access CodeArtifact, I need an authentication token that allows my EC2 instance to access CodeArtifact for 12 hours.
I ran into an error when retrieving the token because my EC2 instance does not have the permission to access my AWS resources by default.
This follows the AWS principles of least priviledge.

### Resolution

To resolve the error with my security token, I set up an IAM policy that grants access to CodeArtifact, then an IAM role I can attach to my EC2 instance.
This resolved the error because my EC2 now has access to request an authorization token from my repository.

It's security best practice to use IAM roles because they provide temporary, automatically rotated credentials, reducing the risk of exposed long-term access keys. 
Roles follow the principle of least privilege, granting only the permissions needed. 
They also enable secure cross-service and cross-account access without hardcoding credentials, improving security and compliance.

---

## The JSON policy attached to my role

The JSON policy I set up grants permissions to authenticate and retrieve artifacts from AWS CodeArtifact. 
It allows actions like `GetAuthorizationToken`, `GetRepositoryEndpoint`, and `ReadFromRepository` to access repository resources. 
Additionally, it includes `sts:GetServiceBearerToken` with a condition for CodeArtifact authentication via AWS STS, ensuring secure access. 
These permissions are essential for developers or CI/CD pipelines to pull dependencies efficiently.

![Image](http://learn.nextwork.org/genuine_navy_mysterious_monkey/uploads/aws-devops-codeartifact-updated_23rp7q8r9)

---

## Maven and CodeArtifact

### To test the connection between Maven and CodeArtifact, I compiled my web app using settings.xml

The `settings.xml` file configures Maven to connect to AWS CodeArtifact by setting the repository URL and authentication details. 
It helps Maven get a temporary token using AWS credentials, allowing it to securely download and use dependencies from CodeArtifact.

Compiling means converting the web app's code into a machine-readable format. 
Maven is my compiler, so I used it to compile my code. While doing this, Maven gathers the needed packages. 
It first checks CodeArtifact, and if the packages aren’t there, it fetches them from the Upstream repository (Maven Central) and stores copies in CodeArtifact.

![Image](http://learn.nextwork.org/genuine_navy_mysterious_monkey/uploads/aws-devops-codeartifact-updated_c17eace8)

---

## Verify Connection

After compiling, I checked my CodeArtifact repository and I noticed 4 pages of packages inside.
This means I successfully stored my web app's dependencies in an artifact repository.

![Image](http://learn.nextwork.org/genuine_navy_mysterious_monkey/uploads/aws-devops-codeartifact-updated_1d79e699)

---

## Uploading My Own Packages

In a project extension, I also decided to become a package publisher, which means publishing my own packages into my CodeArtifact repository. 
This is useful in situations where I might have internal team members developing their own packages and wanting their fellow teammates to have access to it without giving the entire world access. 

To create my own package, I set up a text file and used tar to package up the text file. 
I also generated a security hash because that will give CodeArtifact a way to figure out whether or not the package has been tempered with in transit.
If it has been tampered with, the security hash that CodeArtifact generates will not match up to the security hash that I generate.

To publish the package, I run a CLI command that lets me upload the package I created in CloudShell to my repository.
When I look at the package details in CodeArtifact, I can see the version number, publish date, and even the origin. In this case, the  CodeArtifact repository itself is the origin.

To validate my package, I downloaded it in CloudShell. It installed successfully from CodeArtifact. After unzipping it, I found the text file I had created earlier. This confirmed that CodeArtifact correctly stored and let me retrieve my custom package.

![Image](http://learn.nextwork.org/genuine_navy_mysterious_monkey/uploads/aws-devops-codeartifact-updated_sm12-upload)

---

---
