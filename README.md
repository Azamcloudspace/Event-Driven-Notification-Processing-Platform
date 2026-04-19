#  Event-Driven Notification Processing Platform

##  Project Summary

Designed and implemented a serverless, event-driven notification platform on AWS that processes file upload events in real time and routes notifications to relevant stakeholders based on defined business rules.

The system leverages event-driven architecture principles, automated CI/CD pipelines, and Infrastructure as Code to ensure scalability, maintainability, and consistent multi-environment deployments.

---

##  Key Outcomes

- Built an event-driven processing system using EventBridge and Lambda  
- Automated infrastructure provisioning using CloudFormation  
- Implemented CI/CD pipelines for infrastructure and application updates  
- Designed rule-based notification routing using SNS  
- Enabled multi-environment deployments (dev, staging, prod)  
- Achieved fully serverless architecture with minimal operational overhead  

---

##  Architecture Overview

The platform follows a fully serverless, event-driven design:

```
S3 Upload → EventBridge → Lambda → SNS → Email Notifications
```

![Architecture](/screenshots/Architecture.jpeg)

---

##  System Design

### Core Components

#### Event Source
- **Amazon S3**
  - Stores uploaded files  
  - Emits `Object Created` events  

---

#### Event Routing
- **Amazon EventBridge**
  - Receives S3 events  
  - Filters and routes events to Lambda  

---

#### Processing Layer
- **AWS Lambda**
  - Python-based function  
  - Processes file metadata  
  - Applies business rules  

---

#### Notification Layer
- **Amazon SNS**
  - Publishes notifications to different topics  
  - Sends email alerts to subscribed users  

---

#### Security
- **AWS IAM**
  - Controls permissions between services  
  - Enforces least privilege access  

---

##  Business Logic (Event Processing Rules)

The Lambda function evaluates uploaded files and routes notifications:

- **Large Files (>3MB)**  
  → Routed to `LargeFileTopic`

- **YAML Files (.yaml)**  
  → Routed to `DataTeamTopic`

- **Finance Files (finance/ prefix)**  
  → Routed to `FinanceTeamTopic`

---

##  CI/CD Architecture

This project uses a **two-pipeline DevOps model**:

---

### 1. Infrastructure Pipeline

Responsible for provisioning and updating AWS resources.

**Workflow:**

```
CodePipeline → GitHub(Source) → CodeBuild(Build) → CloudFormation → Environment Deployment
```

**Capabilities:**

**Codepipeline**

- Integrates with services such as GitHub and CodeBuild
- Automatically triggers and executes workflows
- Uses manual approval gates to control promotion between stages
- Implements properly structured IAM permissions across services

**GitHub**

- Repository (Source)

**CodeBuild**

Mutiple Codebuild services are used for the different environments with their individual builspec.yml i.e `dev-buidspec.yml`, `staging-buildspec.yml`, andn`prod-buildspec.yml` respectively , their capabilities are :

- Uploads all nested CloudFormation templates to an S3 bucket for stack referencing
- Deploys the `cloudformation/master/masterstack.yaml` template
- Implements properly structured IAM permissions across services

**Cloudformation**

- Provisions `cloudformation/master/masterstack.yaml` template child stacks resources 


![Pipeline](/screenshots/Screenshot5.png)
Screenshot of Infrastructure Pipeline

---

### 2. Application Pipeline

Handles Lambda code updates independently of infrastructure.

**Workflow:**

```
GitHub(Source) → CodeBuild(Build) → Lambda(Deploy)
```

**Responsibilities:**

**GitHub**

- Repository (Source)

**CodeBuild(Build)**
via `buildspec.yml`
- Copies the app.py file from the app/ directory to the root
- Outputs the `app.py` file as a build artifact

**Lambda(Deploy)**
 
- Updates function without affecting infrastructure  


![Pipeline](/screenshots/Screenshot4.png)
Screenshot of Application Pipeline 

---

##  Deployment Lifecycle

### Initial Provisioning

Infrastructure Pipeline  
→ CloudFormation Deployment  
→ Resources Created (S3, Lambda, EventBridge, SNS, Pipelines)

![Deployment](/screenshots/Screenshot6.png)
Screenshot of Cloudformation Deployment (Prod Environment)

---

### Continuous Delivery

Code Push  
→ Application Pipeline Trigger  
→ CodeBuild (Package Lambda)  
→ Deploy Updated Artifact  
→ Lambda Updated  
→ Event Processing Active  

![Deployment](/screenshots/Screenshot4.png)
Screenshot of Pipeline Trigger 

![Deployment](/screenshots/Screenshot3.png)
Lambda before Trigger 

![Deployment](/screenshots/Screenshot7.png)
Lambda Updated

---

##  Repository Structure

```
.
├── app/
├── ci/
├── cloudformation/
│ ├── child-templates/
│ ├── infrastructure-pipeline/
│ ├── master/
│ └── params/
```

---


##  Automation

- Infrastructure fully defined in CloudFormation  
- Build and packaging handled via buildspecs
- Lambda function updates automated through pipelines  
- Environment-specific parameterization for consistency  

---

##  Monitoring & Observability

- **CloudWatch Logs**
  - Tracks Lambda execution and errors  

- **EventBridge Monitoring**
  - Confirms rule execution and event flow  

- **SNS Delivery**
  - Verifies notification delivery  

---

##  Security Implementation

- IAM roles scoped per service (least privilege)  
- No hardcoded credentials  
- Controlled service-to-service access  
- Secure event-driven communication  

---

##  DevOps Capabilities Demonstrated

- Event-driven architecture design  
- Serverless application deployment  
- CI/CD pipeline implementation  
- Infrastructure as Code (CloudFormation)  
- Multi-environment deployment strategy  
- Automated application delivery  

---

##  Challenges & Resolutions

- **Event filtering complexity**  
  Resolved using EventBridge rules for precise routing  

- **Pipeline separation (infra vs app)**  
  Implemented dual-pipeline model to avoid deployment coupling  

---

##  Future Improvements

- Add retry and dead-letter queue (DLQ) for failed events  
- Implement CloudWatch alarms and alerting  
- Introduce centralized logging and tracing  
- Add event schema validation  
- Implement rate limiting and throttling controls  

---

## Testing Strategy

### Functional Tests
- Upload large file → Verify notification
- Upload .yaml file → Verify notification
- nUpload file to finance/ → Verify notification

![Testing](/screenshots/Screenshot8.png)
Files uploaded to S3 bucket

![Testing](/screenshots/Screenshot10.jpeg)
Large File Screenshot

![Testing](/screenshots/Screenshot11.jpeg)
.yaml File Screenshot

![Testing](/screenshots/Screenshot12.jpeg)
finance/ File Screenshot

## Conclusion

This project demonstrates strong capability in designing and implementing event-driven, serverless systems with automated deployment pipelines. It reflects practical DevOps engineering skills focused on scalability, automation, and real-time processing.
