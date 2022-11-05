[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

<!-- PROJECT LOGO -->
<br />
<div align="center">
    <img src="images/logo.png" alt="The following logo was created by Dall-E 2 with the following description _3d render of the twitter logo being uploaded to an aluminum bucket_">

<h3 align="center">Backing up Twitter</h3>

  <p align="center">
    Backing up twitter tweets became an hot topic in the last couple of days. I've decided to create a simple POC using Serverless components.
    <br />
    <br />
    <a href="https://github.com/aws-hebrew-book/backup-twitter/issues">Report Bug</a>
    ·
    <a href="https://github.com/aws-hebrew-book/backup-twitter/issues">Request Feature</a>
  </p>
</div>


<!-- TABLE OF CONTENTS -->
<details>
  <summary>Table of Contents</summary>
  <ol>
    <li>
      <a href="#about-the-project">About The Project</a>
      <ul>
        <li><a href="#high-level-architecture">High level architecture</a></li>
      </ul>
    </li>
    <li>
      <a href="#getting-started">Getting Started</a>
      <ul>
        <li><a href="#prerequisites">Prerequisites</a></li>
        <li><a href="#installation">Installation</a></li>
        <li><a href="#testing">Testing</a></li>
        <li><a href="#monitoring">Monitoring</a></li>
      </ul>
    </li>
    <li><a href="#contributing">Contributing</a></li>
    <li><a href="#license">License</a></li>
    <li><a href="#contact">Contact</a></li>
    <li><a href="#logo">Logo</a></li>
  </ol>
</details>

## About The Project
Taking a real world issue and trying to tackle it using Serverless component is a good way to learn about Serverless. The following Serverless application is using a scheduled
task that runs once per day and backs up all the twitter handles that you've configured.

After deployment you can configure the relevant twitter handles that you want to backup by changing the value of a parameter called `TwitterAccounts` found under
the [Parameters Store](https://us-east-1.console.aws.amazon.com/systems-manager/parameters?region=us-east-1).

Each day the twitter handles are backed up at 10 AM UTC time. Due to Twitter API restricitons, only the previous day tweets are being backup up. You can find your tweets under S3.
<div align="center">
    <img src="images/s3-bucket.png" alt="Tweets under S3">
</div>

### High level architecture
<div align="center">
    <img src="images/twitter-backup.png" alt="Architecture diagram">
</div>

1. We have an evenbridge as a cron scheduler.
2. A Lambda is being triggered everyday at 10AM UTC.
3. In order to pull the configuration, the [AWS Parameters and Secrets Lambda Extension](https://docs.aws.amazon.com/systems-manager/latest/userguide/ps-integration-lambda-extensions.html) is used.
4. Each twitter handle is being pushed as a seperate mesage into SQS.
5. Using the [batch processing utility](https://awslabs.github.io/aws-lambda-powertools-python/2.1.0/utilities/batch/) in the Python Lambda Power tools. each message is being processed
6. For each handle we are making a twitter api call to get the twitter account id and then the tweets from last day.
7. The twitter bearer token, which is required for authentication, is pulled from AWS secret manager using [AWS Parameters and Secrets Lambda Extension](https://docs.aws.amazon.com/secretsmanager/latest/userguide/retrieving-secrets_lambda.html)
8. Tweets are written into AWS Kinesis Firehose which writes them into S3. 
9. [Dynamic partitioning](https://docs.aws.amazon.com/firehose/latest/dev/dynamic-partitioning.html) is used, therefore the files created under S3 have handle prefix.


## Getting started
### Prerequisites
* Make sure your machine is ready to work with [AWS SAM](https://aws.amazon.com/serverless/sam/)
* Create a [twitter API account](https://developer.twitter.com/en/docs/twitter-api/getting-started/getting-access-to-the-twitter-api)
* And save locally your `Bearer Token`, you should receive it at the end of the registration process

### Installation
* Clone this repository.
* Run `sam build` and then `sam deploy --guided`. Accept the default values, except for 
** _Parameter TwitterBearerToken_ - here paste the token value you've recevied from twitter. 
** _Parameter TwitterAccountsValues_ - Choose the twitter handles you want to backup. Of course, you can use the default here.

After the deployment is complete you can always change the twitter handles you want to backup by changing the value found under https://us-east-1.console.aws.amazon.com/systems-manager/parameters?region=us-east-1

### Testing
* You can test application manully by executing the `ScheduleBackupFunction` Lambda directly from the console.

### Monitoring
Monitoring is done by using [Lumigo](https://platform.lumigo.io/auth/signup)

## Contributing

Contributions are what make the open source community such an amazing place to learn, inspire, and create. Any contributions you make are **greatly appreciated**.

If you have a suggestion that would make this better, please fork the repo and create a pull request. You can also simply open an issue with the tag "enhancement".
Don't forget to give the project a star! Thanks again!

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request


<!-- LICENSE -->
## License

Distributed under the Apache License Version 2.0 License. See `LICENSE` for more information.

<!-- CONTACT -->
## Contact

Efi Merdler-Kravitz - [@TServerless](https://twitter.com/TServerless)



## Logo
The project's logo was created by Dall-E 2 with the following description _3d render of the twitter logo being uploaded to an aluminum bucket_


<p align="right">(<a href="#readme-top">back to top</a>)</p>