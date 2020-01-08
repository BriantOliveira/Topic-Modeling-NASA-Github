# Topic-Modeling-NASA-Github
Repository topic-modeling, repository recommendation engine, code author clustering.


## How applying digital transformation to code hosting could represent a way to increase efficiency and effectiveness

The rise of the version control system has led to an increase in the number of open-source projects available online. As a result, finding relevant projects have become more and more difficult for developers. Additionally, NASA has many different internal and public code hosting instances, which make finding already existing NASA code difficult. Code recommendation provides a new way to solve both these issues. We researched what others have done in this area and attempted a prototype using NASA created open-source code projects on the github.com/nasa account.


### Long-term Vision
Before we dive in more into our initial project, we want to clarify what future digital transformation might look like for code, the value proposition, and the range of people who could benefit from the NASA. 

Our long-term vision is to be able to answer the following questions inside NASA :
- Who writes similar code to me? 
- How has a given library been used before? 
- Who should be invited to an upcoming “Community of Practice” meeting? 
- How much has the use of a given library changed over time? 
- What framework should I use to maximize the chance of reuse for this project?

Developers at NASA are spread across a range of centers, missions, and functional groups. Information exchange between developers is extremely limited and relies entirely on interpersonal channels such as talk, email, powerpoints, etc. This is inefficient and leads to similar projects being worked on across the agency as well as limited reuse of code that has been already written. Time to finish projects increases when different teams working related problems can’t reuse code.  

##### We propose information transfer and code reuse can increase if the code repositories themselves are viewed as a data asset to be managed for maximal value.

Code repositories have the following attributes that can be viewed as raw data: 

- Dependencies: 
  - Lists of what other code libraries or modules are being pulled in and used.
- Keywords
  - (keywords, called topics on GitHub, that describe languages used, what the code does, and how it is organized), 
- Source code 
  - (the actual code which has names for variables, methods, etc. that can be extracted and analyzed via natural language processing). 

Each of these three components, either individually or in combination, can be used as data for machine-learning approaches that recommend code to users based on similarities across a large number of projects.

For now, our goal is to recommend code written by people at NASA. A related goal, currently being worked by others, not at NASA, is to recommend open-source code or code snippets from public sources like github.com to users. At some future point, we might also consider suggesting public open-source code to internal NASA users.

##### Code is widely distributed in many different public and private code hosting systems

Internally, code exists across a large number of gitlabs, bitbuckets, and two Github instances as well as many older directories accessed via FTP. Externally, code is largely on github.com, gitlab.com, and bitbucket.com. Some older projects again are accessed via FTP and not inside a version control hosting system. Each of these systems has its own APIs and different ways of accessing data directly as well as 3rd party data aggregators who have taken the data out and put into larger aggregate forms. 

#### Moving towards an enterprise view of internal NASA code but not there yet

Although NASA has many internal and external code repositories hosting instances spread across many organizations, there isn't yet a fully stood up centralized system that controls or tracks all these repositories. This makes it very challenging for developers to find helpful and applicable resources and limits the formation of an effective development community within NASA that can exchange information and code across organizational boundaries. 

There is a service, currently in an early stage, being built that could link up all these different internal code hosting platforms and provide an enterprise view. The Searchable Code Agency Repository or SCAR database scans several internal and public code hosting platforms at NASA and extracts repository information into a single database. The original reason for SCAR was to have Black Duck, a 3rd party tool, run on each repository and confirm licenses are open source or otherwise being followed. As part of this task, the Black duck has to find all the dependencies, opening up the possibility of having all the dependencies on all internal NASA code as a data source to understand and organize NASA code. SCAR is currently under development, and although the future of SCAR is very promising, the functionality of scrapping all the host instances and extracting dependencies doesn't exist yet. 

### Approaches by External Parties: Topic-modeling, source code as data, and dependency scraping from Github

As mentioned before, there is various companies and projects that are attempting to achieve similar code snippet or code repository prediction abilities on public open-source code using different methods. We will explain some of these different methods and how different parts of code repositories can be used to help achieve our goal.

### Topic Modeling on Source Code

Anyone familiar with GitHub is familiar with the “topics” tag bar that appears below a repository title on the front page of every code repository.



There is also “Topic Modeling”, a Machine Learning subdomain which is devoted to extracting “topics” from a collection of documents. Each document is represented as a model of a collection or a bag-of-words in the form of a histogram or set of occurring words containing frequencies. 

Topic Modeling on Github repositories is a fairly new concept, and numerous companies and open source projects are exploring this realm such as Github, Source{d}, Bigartm, Libraries.io, and others. 

Normally, topic modeling is applied to text documents. There are many different algorithms used  to do topic modeling, the most famous is the Latent Dirichlet Allocation (LDA).

“LDA is a generative statistical model that allows sets of observations to be explained by unobserved groups that explain why some parts of data are similar. For example, if observations are words collected into documents, it posits that each document is a mixture of a small number of topics and that each word’s presence is attributable to one of the document’s topics.” 
- Li, Susan, and Susan Li. “Topic Modeling and Latent Dirichlet Allocation (LDA) in Python.” Towards Data Science, Towards Data Science, 31 May 2018.

The approach of using Topic Modeling in code repository is to treat every repository as a bag-of-words and then apply the Additive Regularization of Topic Models methodology (ARTM). There could be some interesting edges cases, utilizing code as a bag-of-words, it will include short words of code documentation and commits, and also we can’t assume that everyone will commit and document their code properly.

#### Difficulties and limitations
Probabilistic models such as LDA exploit statistical inference to discover latent patterns of data. In short, they infer model parameters from observations. For instance, there is a black box containing many balls with different colors. You draw some balls out from the box and then infer the distributions of colors of the balls. That is a typical process of statistical inference. The accuracy of statistical inference depends on the number of observations. LDA, when used in short documents, can be deceiving, and can cause data sparsity.

#### Dependency tracking
An alternative to looking at the code itself or comments in the code is to look only at the dependencies each code repository uses. A reasonable assumption is similar code will use similar dependencies. We can use this assumption to make recommendations, create clusters of similar code, and undertake analysis on what types of code are being written.  The idea is to go through every repository and collect dependencies as metadata. Recent advances in dependency scraping by 3rd parties such as Libraries.io allows code dependencies to be extracted across a range of languages. Libraries.io has scraped dependencies from  open-source code from Github, GitLab, and Bitbucket and made that data available on Google BigQuery. Libraries.io itself is an open-source project that has indexed data from over 3,720,309 packages from 36 different package managers. They monitor package releases, analyzing each project repository, community, and documentation. 

#### Current limitations with dependency tracking
As the SCAR database only tracks a portion of NASA’s code hosting instances and doesn’t yet have dependencies available for export via their API, we examined what we could do with open-source code projects listed on NASA’s github.com/nasa org account.

After attempting to extract all the dependencies from these repositories via libraries.io extracts on google BigQuery, we noticed a few repositories (namely including C and C++ repositories) were failing to give dependencies results. As we were analyzing these repositories, we noticed that Libraries.io utilizes Github GraphQL API and Github dependency tracking currently is limited by programming language. Specifically, languages like C++ are not included. Additionally, the only way that GitHub is able to track certain projects is if a repository contains specific package management files in the correct format. Some repositories do not have their dependencies in the standard format. Lastly, some repositories don not even list dependencies. Sometimes this is because the repository is just readme and text files. Other times this is due to decisions by the authors to not include dependencies. This is an important limitation for both the public github.com and the internal enterprise GitHub instance at developer.nasa.gov. 

We were partially able to get over these limitations by doing manually grabbing some dependencies that weren’t in a form GitHub tracks.

Due to these limitations, we were limited to getting dependencies from 45  repositories on github.com/nasa. 

#### Concept extraction from project descriptions & READMEs 

Another method to predict similar code repositories is to extract or predict keywords based on text descriptions, either found in other systems that aggregate information about code repositories or in the text of README files, that exist within the repositories themselves. In this approach, natural language processing is used to tag repositories with high-level keywords that are likely to be similar in similar code repositories. A separate project on the team has already done this for the NASA open-source code projects listed on code.nasa.gov. If you go to the site, you’ll see there are both human-generated tags and A.I. generated tags for each project. Those tags are from a list of 7,000 concepts NASA Science Technology and Information program (STI) has tracked across several million published papers and reports. These A.I. generated tags are one way to speed up finding and exploring similar code repositories, at least within NASA’s open-source code. A demo page for NLP tagger API can be found here. The code used to call the STI NLP API and apply tags to the project descriptions saved on code.json can be found here and here. 

This approach, while generally successful, has its limitations as well. First, not all projects have READMEs or descriptions that live outside the repository that can be programmatically fed into an NLP tagger API. Secondly, some projects on code.nasa.gov do not have A.I. generated tags as the amount of text in their descriptions was too small to generate reliable tags. 


#### Constraints on expending this prototype:
Currently, there are a few limitations on code.nasa.gov, that constrain what we can do:

- As previously mentioned, not every repository project had package managers or defined dependencies in a supported language file format.

- Hosting services still have limited dependencies monitoring. As an example, Github has difficulties tracking and generating dependency graphs on projects that are written in certain programming languages, such as C and C++.

- In terms of internal codebases, the Black Duck dependencies results are not available on SCAR yet. 

- Not every repository hosting services are implemented on SCAR yet, such as Gitlab or Bitbucket.

#### Our dataset for this project

For this project, given the limitations on our ability to get internal code dependencies and our interest in working with NASA written code, we stuck to using the open-source code on code.nasa.gov and the portion of those projects listed on github.com/nasa in particular.

Currently, with a BigQuery instance, we are able to search through 3.74M packages by license, language, keyword and etc and track 137 open-sourced repositories listed on code.nasa.gov. We got data that included fork counts, what region our contributors are from, what is the status of the project, size, rank and etc. However, we were only able to get dependency data from 45 open-source NASA repositories due to the limitations mentioned above.

#### Analysis Results: What does the distribution of dependencies tell us?
Let's dive into the data analytics aspect of our prototype. 

Once we instantiated the BigQuery instance, we wanted to understand the data we’re working on. The data was distributed in 7 different packages/tables, and each one of them contained different contents: 
	- dependencies:
		71,884,555 rows or observations
		12 columns or features
		Table size: 7.47 GB
	- projects:
		2,556,269 rows or observations
		21 columns or features
		Table size: 664.81 MB
	- projects_with_dependencies
		2,556,311 rows or observations
		61 columns or features
		Table size: 1.21 GB
	- repositories:
		30,705,634 rows or observations
		40 columns or features
		Table size: 6.79 GB
	- repositories_dependencies
		231,231,809 rows or features
		13 columns or features
		Table size:  28.07 GB
	- tags
		46,251,665 columns or observations
		9 columns or features
		Table size: 5.5 GB
	- versions
		11,804,946 columns or observations
		8 columns or features 
		Table size: 856.86 MG

In order to provide metrics on our predictions, we started by translating three simple questions into SQL queries:

- What are the repositories, avg project size and avg number of stars?


- What are the top dependencies per platform?


- What are the top unmaintained or deprecated projects?


### Feature Engineering for Clustering Projects Based on Dependencies

#### Principal Components Analysis (PCA)
The procedure to convert a set of observations of possibly correlated variables into a set of values of linearly uncorrelated variables is called principal components.

#### K-Means
It is a clustering method of vector quantification. K-Means clustering aims to partition n observations into k clusters in which each observation belongs to the cluster with the nearest mean, serving as a prototype of the cluster.

#### Agglomerative hierarchical Clustering
Agglomerative clustering, the more common approach, means that the algorithm nests data points by building from the bottom up. In other words, each data point is its own cluster and then they are joined together to create larger clusters. There are a few other data results we would like to highlight as well.



### What things have to change for this prototype to scale up?

- A few departments in the organization still do not use version control and archive scripts and codebases in their local storage, which makes it very difficult to share resources with others in the organization. It also runs the risk of losing files and non-compliance with license constraints...

- Every project should contain a package manager or defined dependencies in a supported file format defined by the repository hosting provider. 

- In order to make this prototype effective on internal codebases, we will need to have the Black Duck dependencies results inserted into SCAR's database,

- SCAR’s API endpoint needs to include dependencies. 

### What value do we think we could get from this small prototype?

The prototype has demonstrated the current limitations to a few different means of programmatically predicting similar code repositories and has allowed us to identify what needs to be done in order to achieve our desired end results. Additionally, this prototype has helped demonstrate to our team the importance of following common programming conventions. 

In more detail:

Within the repositories on github.com/nasa that we can track the dependencies, we can better understand and monitor changes that are made in the dependencies. 

1- We could answer questions such as: 
Are any of these dependencies deprecated over time? 
How many versions does this give dependency has since the last update of our project?
Are there any other codebases that are using similar dependencies?
 What are some of the keywords and tags associated with these dependencies?

2 - It also helps us understand better what region our contributors are from and what region has our open-source projects impacted by analyzing forks and stars on Github. This allows us to quantify the impact of our outreach to the public via open-source code. 

3 - t helps us find other projects made by the community that utilizes NASA's open-source code, allowing us to further measure what people have been doing with NASA's data and etc.

Speaking more broadly, dependencies, source code, and readme.md files are all different sources of data that could be used to develop expand this prototype. They each have their unique strengths and weaknesses. 




### Conclusion:
Throughout the prototyping journey of this project, we were able to understand better some of our current restrictions. Out of the 137 NASA's open-source projects, we were able to track dependencies and interdependencies of 45 projects. Although it wasn't the result expected, it was enough to allow us to do this small prototype experiment and it helped find some very interesting results and to understand what we need to change in order to achieve the end goal and the limitations of the tools and services we are using.















