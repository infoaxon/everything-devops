# Proposed Github Structure and Release Mechanism for Honda

The document below briefly proposes code control, release mechanism, and version numbering for the Honda project.

At this stage this is a proposal and the intention is to use this as a baseline for discussion with the team including - Analysts, Developers, Testers, Infrastructure team, Project Manager, DevOps team, Release Management team and Management.

Once the process has been agreed, the steps/process maybe automated via relevant tools (Jenkins, ansible, gradle etc).

> IMPORTANT:
>
> This document does not assign responsibilities as this stage. Once the process/design has be debated and thrashed out, the dependant RACI matrix would be designed/produced to allow implementation and handover.

<div style="page-break-after: always;"></div>
<br>

## The following branches along with access and security controls are proposed.

| #   | Branch  | Description                                                                                                      | Control                                                                                                                                                                                                               |
| --- | ------- | ---------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | master  | The main branch for the project. The code here is always runnable and releasable.                                | Protected branch. Only pull requests allowed. Release Manager responsible for merging pull requests.                                                                                                                  |
| 2   | dev     | Development branch for the project. Only code that works is merged from feature branches into dev branch.        | Protected branch. Only pull requests allowed. Team Lead responsible for merging pull requests.                                                                                                                        |
| 3   | feature | Branch(s) for each feature assigned to a single developer. Postfix with feature name - e.g <feature/singlelogin> | Day to day commits by the developer until code is ready. The developer make a pull request for code to be merged into the develop branch. Team Lead responsible for reviewing pull request and merging tin dev branch |
| 4   | hotfix  | Branch to handle hotfixes. name should be appended by Jira defect number or a short id of the bug.               | On completion and testing these are merged into dev and master branch. They might be merged into env branches as well.                                                                                                |
| 5   | uat     | Environment specific branch                                                                                      | Managed by Release Manager                                                                                                                                                                                            |
| 6   | prod    | Environment specific branch                                                                                      | Managed by Release Manager                                                                                                                                                                                            |

> IMPORTANT:
>
> All releases emanate from **master** branch.

<div style="page-break-after: always;"></div>
<br>

# Proposed Release Mechanism for Honda

Following a successful build, deployment on the development environment and successful testing in the development environment, the project needs to cut a Release Candidate(RC) and promote the frozen release into the SIT environment. The steps below list the process steps that need to be achieved to cut/make a release. These steps may be automated using jenkins or other suitable tools.

1. Tag and make a release in gitlab. Tag and release name is synonymous in the git world. In effect Tag and Release name/number can be used interchangeably. For the purpose of this document we will use release name going forward. The release number/naming format have been explained in a separate section within this document.

2. Gitlab release process zips up the code and includes that as an asset as part of the release. In future if there is a need to roll back to the release code, the team may either download the zipped code or checkout the named release from Gitlab. Also, please include release notes, documentation and installation instructions along with the release assets. This can be done in Gitlab by adding additional assets to the release.

3. Upload the released artifacts into artifactory. There should be separate group/folder in the artifactory repository for release candidates and stable releases/releases (explained in the next section). The artifacts for a release normally includes - jars, source zip, release notes, install instructions and documentation.

4. Deploy the released assets onto SIT env. This will only include deploying the jars onto the SIT environment.

5. Check if the deployment is successful by running basic tests or any other defined automated tests.

6. Send out notification of successful release ito SIT and readiness for testing. Include the artifactory link to the jars and code/documentation etc for the release in the notification.

> NOTE:
>
> This is a simplistic Release Management process that works with the proposed code control flow as defined in the git flow in the previous section.
> This document does not assign individual responsibilities as yet. Individual responsibilities/ownership can be defined based on the steps above.

<div style="page-break-after: always;"></div>
<br>

# Proposed Version Numbering for Honda

The following proposed definitions will be used in the context of the document and Honda release process.

-   <b>Release Candidate</b> - A frozen version/release of the code with potential to be a stable release publishable to the customer.
-   <b>Release</b> - A fully test and releasable product release that can be sent to the customer

## Generic Release Numbering

It is proposed the release numbering be in 4 numeric values separated by "." dots. The number can be prefixed by a short product name.The format is as follows:

<pre>
productname-xx.yy.zz.bbb
</pre>

Where the symbols mean:

-   <b>xx</b> - Major release number. This is subjective, but normally done when there are major new additions to the fuctionality.
-   <b>yy</b> - Minor release number.
-   <b>zz</b> - Patch.
-   <b>bbb</b> - Build number.

## Release Candidate Naming Convention

Release Candidates may be pre-fixed by greek words to ensure visibility and identification of code under test. It is proposed the following order be used:

-   alpha
-   beta
-   gamma
-   delta
-   epsilon
-   zeta

and so on.

Promotion of version name from one to another for example from alpha to beta can be controlled by defining clear quality and number of bugs in a release.

For example yoy may choose to define a policy that if a RC (e.g alpha-inhonda-1.4.22.168) has more than 40 bugs then it should be promoted to the next greek alphabet and undergo the entire test process from SIT onwards again. This is again subjective and can be managed from case to case basis.

Once the release has passed the test process the greek alphabet is dropped and the release is promoted to a stable release.

<div style="page-break-after: always;"></div>
<br>

# Proposed Responsibility Chart for Honda

| #   | Task                                                                                                      | Tool/s                            | Responsible           | Mechanism                      | Trigger                                                          | Notes                                              |
| --- | --------------------------------------------------------------------------------------------------------- | --------------------------------- | --------------------- | ------------------------------ | ---------------------------------------------------------------- | -------------------------------------------------- |
|     | REGULAR DEVELOPMENT                                                                                       |                                   |                       |                                |                                                                  |                                                    |
| 1   | Create feature branch                                                                                     | git                               | Developer             | Manual                         | Adhoc                                                            |                                                    |
| 2   | Code development and checkin to feature branch                                                            | Editor/IDE, git                   | Developer             | Manual                         | Adhoc                                                            |                                                    |
| 3   | Raise "Pull Request" from feature branch into dev branch on completion of feature development/ defect fix | git                               | Developer             | Manual                         | Adhoc                                                            |                                                    |
| 4   | Quality check pull request and mergecode into dev branch                                                  | git                               | Development Team Lead | Manual                         | Adhoc                                                            |                                                    |
| 5   | Build code, save in repository, deploy into development environment                                       | Jenkins, git, gradle, artifactory | Development Team Lead | Automated via Jenkins pipeline | Code merge into development branch in git                        |                                                    |
| 6   | Code test, quality assurance                                                                              | Selinium, postmaster, jmeter etc  | QA                    | Manual/Automated               | New code build and code deploymemnt into development environment |                                                    |
| 7   | Defect raised against code in development environment                                                     | Jira                              | QA                    | Manual                         |                                                                  |                                                    |
| 8   | Fix defect against code deployed in development environment. Day to day code checkin into feature branch  | IDE, git                          | Developer             | Manual                         | Defect raised in Jira                                            |                                                    |
| 9   | Go to step 3 and iterate until all defects are fixed                                                      |                                   |                       |                                |                                                                  |                                                    |
|     |                                                                                                           |                                   |                       |                                |                                                                  |                                                    |
|     | MAKE RELEASE for Release Candidate                                                                        |                                   |                       |                                |                                                                  |                                                    |
| 10  | Cut release in git. Version naming and numbering as per agreed process                                    | git                               | Release Manager       | Manual/Automated               | QA pass from development environment                             | RC Release can be automated via a Jenkins pipeline |
| 11  | Attach additional assests to release as per defined process                                               | git                               | Development Team Lead | Manual/Automated               |                                                                  |                                                    |
| 12  | Deploy code into SIT                                                                                      | ansible, Jenkins etc              | Release Manager       | Manual/Automated               |                                                                  |                                                    |
| 13  | Notify stakeholders                                                                                       | email                             | Release Manager       | Manual/Automated               |                                                                  |                                                    |
|     |                                                                                                           |                                   |                       |                                |                                                                  |                                                    |
|     | SIT TEST and DEVELOPMENT                                                                                  |                                   |                       |                                |                                                                  |                                                    |
| 14  | Carry out agreed tests in SIT. Raise defects in Jira against release number                               | Jira and supporting QA tools      | QA                    | Manual/Automated               | RC deployed into SIT environment                                 |                                                    |
| 15  | Repeat steps 3 onwards until all defects are cleared                                                      |                                   |                       |                                |                                                                  |                                                    |
| 16  | Deploy release into UAT once all defects are fixed. Follow steps for RC                                   |                                   | Release Manager       |                                |                                                                  |                                                    |
|     |                                                                                                           |                                   |                       |                                |                                                                  |                                                    |
|     | MAKE STABLE RELEASE                                                                                       |                                   |                       |                                |                                                                  |                                                    |
| 17  | Pull request from dev to master branch                                                                    |                                   | Development Team Lead | Manual                         | QA pass from UAT                                                 |                                                    |
| 18  | Quality check request, merger code and add additional release assets                                      |                                   | Release Manager       | Manual                         | Pull request                                                     |                                                    |
| 19  | Build code for stable release - build, tag, upoad into artifactory (in a separate group)                  |                                   | Release Manager       | Manual/Automated               |                                                                  | Can be automated via Jenkins                       |
| 20  | Send out "Stable release ready for production" notification to stakeholders                               |                                   | Release Manager       | Manual/Automated               |                                                                  | Can be automated via Jenkins                       |

### END OF DOC
