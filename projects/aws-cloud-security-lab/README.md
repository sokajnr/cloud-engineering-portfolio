AWS Cloud Security Lab — Amazon S3 & IAM
Author: Andrew Odeyemi
GitHub: github.com/sokajnr
AWS Region: US East (Ohio) — us-east-2
Project Date: November 20, 2026
Project Status: Infrastructure Configured | Preliminary IAM Simulation Completed | Runtime Validation Pending
Project Overview
This hands-on cloud security project demonstrates the implementation of foundational security controls using Amazon Web Services (AWS), with a focus on secure cloud storage, identity and access management (IAM), and least-privilege access.

I configured a private Amazon S3 bucket, implemented encryption at rest and public access restrictions, developed a custom IAM read-only policy, and created an IAM role trusted by Amazon EC2.

The project demonstrates practical experience with:
- AWS cloud infrastructure configuration
- Secure Amazon S3 storage
- Identity and access management (IAM)
- Least-privilege security principles
- Role-based access control
- JSON-based security policies
- Cloud security architecture and documentation
Project Goal: Design and document a secure AWS storage environment where an authorized AWS workload can access only the resources and actions required.
Technologies and AWS Services
Technology	Purpose
Amazon S3	Secure cloud object storage
AWS IAM	Identity and access management
IAM Policies	Resource-level permission management
IAM Roles	Temporary, role-based AWS access
Amazon EC2	Trusted service for the IAM role
JSON	IAM policy and trust relationship configuration
GitHub	Version control and technical documentation
AWS Amplify	Hosting for my separate cloud portfolio website


Project 1: Secure Amazon S3 Bucket
Objective
Create a private cloud storage environment that incorporates AWS security best practices for data protection and access control.
Implementation
I created and configured an Amazon S3 bucket with the following settings:

Bucket Name: sokajnr-cloud-security-lab-2026
AWS Region: us-east-2
Security Configuration	Setting
Block Public Access	Enabled
Access Control Lists (ACLs)	Disabled
Default Encryption	SSE-S3
Bucket Accessibility	Private
Bucket Versioning	Disabled initially


Security Controls Implemented
1. Block Public Access

Enabled S3 Block Public Access to help prevent unintended public access to stored objects.

2. Access Control Lists Disabled

Configured S3 Object Ownership with ACLs disabled to simplify permissions management and rely on policy-based access controls.

3. Server-Side Encryption

Configured Amazon S3-managed encryption keys (SSE-S3) to provide encryption at rest for stored objects.

4. Private Cloud Storage

Maintained private bucket access rather than exposing objects publicly.
Security Outcome
Successfully created a private S3 storage environment with public access restrictions and encryption at rest.

This demonstrates practical knowledge of AWS storage security and cloud data protection.
Project 2: Custom IAM Read-Only Policy
Objective
Apply the principle of least privilege by granting only the permissions required to list and retrieve objects from a specific S3 bucket.
Implementation
Created a customer-managed IAM policy:

Policy Name: Sokajnr-S3-ReadOnly-Policy

The policy grants two S3 permissions:
IAM Action	Description
s3:ListBucket	List objects within the designated S3 bucket
s3:GetObject	Retrieve objects stored in the designated S3 bucket


IAM Permissions Policy (JSON)
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "VisualEditor0",
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::sokajnr-cloud-security-lab-2026/*",
        "arn:aws:s3:::sokajnr-cloud-security-lab-2026"
      ]
    }
  ]
}
Least-Privilege Security Design
The policy restricts its granted permissions to one S3 bucket and its objects.

It does not grant permissions for:
- s3:PutObject — Uploading objects
- s3:DeleteObject — Deleting objects
- s3:PutBucketPolicy — Modifying bucket policies
- s3:CreateBucket — Creating new buckets
- Administrative IAM operations
Security Note: These restrictions describe the permissions granted by this policy. Effective AWS access also depends on any other applicable identity policies, resource policies, permission boundaries, and organization-level controls.
Security Outcome
Successfully created a custom IAM policy that grants limited, resource-specific S3 read permissions.

This demonstrates the ability to design granular AWS permissions rather than relying on overly permissive administrative policies.
Project 3: IAM Role for Amazon EC2
Objective
Establish a secure IAM role that an authorized EC2 instance can use to access a private S3 bucket without requiring permanent access keys stored on the instance.
Implementation
Created the following IAM role:

Role Name: Sokajnr-S3-ReadOnly-Role
Trusted AWS Service: Amazon EC2
Attached Policy: Sokajnr-S3-ReadOnly-Policy
IAM Trust Policy (JSON)
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "sts:AssumeRole"
      ],
      "Principal": {
        "Service": [
          "ec2.amazonaws.com"
        ]
      }
    }
  ]
}
How the Role Works
The IAM role establishes a trust relationship allowing the Amazon EC2 service to assume the role for an authorized instance.

When properly associated with an EC2 instance through an instance profile, the role can provide temporary AWS credentials.

The attached permissions policy determines which AWS actions those credentials are authorized to perform.
Security Benefits
- Reduces the need to store permanent AWS credentials in application code.
- Supports temporary credential-based authentication.
- Separates service trust relationships from resource permissions.
- Applies least-privilege access to a specific S3 bucket.
- Demonstrates AWS role-based access control.
Implementation Status
The IAM role and its associated instance profile have been created successfully.

No EC2 instance has been launched or associated with the role. Runtime access testing remains a future project phase.
Cloud Security Architecture
The following diagram illustrates the intended access-control design.
                  Amazon EC2
                 Trusted Service
                       |
                       v
                  AWS IAM Role
           Sokajnr-S3-ReadOnly-Role
                       |
                       v
                IAM Permissions
           Sokajnr-S3-ReadOnly-Policy
                       |
              +--------+--------+
              |                 |
              v                 v
         s3:ListBucket     s3:GetObject
              |                 |
              +--------+--------+
                       |
                       v
                Amazon S3 Bucket
       sokajnr-cloud-security-lab-2026
                       |
              +--------+--------+
              |                 |
              v                 v
         SSE-S3 Encryption  Block Public Access
Architecture Note: This diagram represents the configured IAM trust and permission model. EC2-to-S3 connectivity has not yet been deployed or validated.
IAM Security Simulation Results
Testing Date: November 20, 2026
Testing Tool: AWS IAM Policy Simulator
Selected Role: Sokajnr-S3-ReadOnly-Role
Attached Policy: Sokajnr-S3-ReadOnly-Policy
Scope: Preliminary IAM policy simulation; bucket-specific and live workload validation pending
Observed Test Results
Test	S3 Action	Expected Result	Observed Simulator Result	Outcome
1	s3:ListBucket	Allowed	Allowed — 1 matching statement	Matched expectation
2	s3:GetObject	Allowed	Allowed — 1 matching statement	Matched expectation
3	s3:PutObject	Denied	Implicitly denied — no matching statements	Matched expectation
4	s3:DeleteObject	Denied	Implicitly denied — no matching statements	Matched expectation


Test Procedure and Findings
I selected the custom EC2 IAM role in AWS IAM Policy Simulator and tested four Amazon S3 actions individually. The two read actions returned Allowed, while the upload and delete actions returned Implicitly denied. All four reported results matched the intended read-only permission model in the simulation configuration used.

During testing, an initial ListBucket simulation returned Denied by AWS Organizations. A subsequent test with the relevant SCP selection excluded from the simulation returned Allowed. This shows why identity-based permissions alone do not establish effective access: AWS Organizations service control policies can impose additional restrictions.
Limitations and Interpretation
- The results table displayed * under Simulation Resource rather than the specific bucket or object ARN. Consequently, these tests are recorded as preliminary, not conclusive resource-specific authorization validation.
- The simulator also displayed a bucket or object ARN under Resource Policies at different points; that display alone does not establish that the same ARN was used as the simulation resource.
- The simulator's SCP inclusion state and the applicable organizational restrictions must be confirmed before drawing conclusions about effective permissions.
- No EC2 instance has been launched or attached to this role, and no live EC2-to-S3 request has been tested.
- IAM Policy Simulator results do not guarantee identical behavior for live AWS requests.
Remaining Validation Tasks
- [x] Simulate s3:ListBucket and record the result.
- [x] Simulate s3:GetObject and record the result.
- [x] Simulate s3:PutObject and record the result.
- [x] Simulate s3:DeleteObject and record the result.
- [x] Identify an AWS Organizations SCP-related denial during testing.
- [ ] Repeat simulations using explicit bucket and object ARNs, where supported.
- [ ] Confirm which SCPs apply and document their effect on the selected role.
- [ ] Validate access using an authorized AWS workload, with cost and security precautions.
- [ ] Add sanitized screenshots as supporting evidence.
Conclusion: The observed simulator decisions are consistent with the intended read-only IAM policy. Bucket-specific effective permissions and live workload access have not yet been verified.
Project Implementation Checklist
Completed
- [x] Created AWS account and configured multi-factor authentication (MFA).
- [x] Created Amazon S3 bucket in the Ohio AWS Region.
- [x] Enabled S3 Block Public Access.
- [x] Disabled S3 Access Control Lists.
- [x] Configured SSE-S3 encryption.
- [x] Created custom IAM read-only policy.
- [x] Restricted policy resources to the designated S3 bucket.
- [x] Created an IAM role trusted by Amazon EC2.
- [x] Attached the custom IAM policy to the EC2 role.
- [x] Created the role's associated EC2 instance profile.
- [x] Ran four preliminary IAM Policy Simulator tests.
- [x] Recorded read-action Allow and write/delete implicit Deny results.
- [x] Documented an AWS Organizations SCP-related simulation denial.
- [x] Documented preliminary results and testing limitations.
- [x] Linked this project from the live AWS Amplify portfolio website.
Pending
- [ ] Validate bucket-specific and object-specific ARNs in the simulator.
- [ ] Confirm the effect of applicable AWS Organizations SCPs.
- [ ] Perform authorized runtime access testing.
- [ ] Add sanitized screenshots of AWS configurations and test results.
- [ ] Publish final end-to-end security validation results.
Technical Skills Demonstrated
AWS Cloud Infrastructure
- Amazon S3 configuration and management
- AWS IAM administration
- IAM role and policy configuration
- Cloud resource security settings
Cloud Security
- Principle of least privilege
- Identity and access management
- Role-based access control
- Resource-level permissions
- Encryption at rest
- Private cloud storage
- AWS service trust relationships
Engineering and Documentation
- JSON security policy development
- Cloud security architecture
- GitHub version control
- Technical project documentation
- Security validation planning
Key Lessons Learned
This project helped reinforce several important AWS cloud security concepts.

1. Identity and permissions are separate components.

IAM roles establish which entities can assume an identity, while permissions policies determine which actions that identity is authorized to perform.

2. Least privilege reduces unnecessary access.

Resource-specific permissions help limit access to the AWS services and resources required for a particular workload.

3. Private storage is an important security foundation.

S3 Block Public Access, disabled ACLs, and encryption at rest provide foundational controls for protecting stored data.

4. IAM roles support temporary credentials.

Using IAM roles with supported AWS services reduces reliance on long-lived access keys.

5. Security configurations should be validated.

Creating IAM policies is only part of secure cloud engineering. Preliminary simulator testing produced the expected read-only results, but resource-specific testing and live access validation remain necessary. AWS Organizations SCPs may further restrict effective access.
Future Project Enhancements
Planned improvements include:
1. Complete resource-specific IAM Policy Simulator testing and investigate applicable SCP restrictions.
2. Validate the IAM role through an authorized AWS workload.
3. Explore S3 bucket versioning and access logging.
4. Evaluate AWS CloudTrail for auditing and monitoring.
5. Investigate additional IAM security controls.
6. Add screenshots and documented testing evidence.
7. Expand the portfolio with additional AWS security projects.
Related Project: AWS Cloud Portfolio Website
In addition to this security lab, I deployed a professional cloud engineering portfolio using AWS Amplify Hosting and GitHub.

Live Portfolio Website:
https://main.d229ku10psc718.amplifyapp.com

GitHub Repository:
https://github.com/sokajnr/cloud-engineering-portfolio

The portfolio demonstrates static website hosting, GitHub integration, and automated deployment through AWS Amplify. It now includes a dedicated project link to this AWS Cloud Security Lab.
About the Author
Andrew Odeyemi
Bachelor of Science in Cybersecurity Technology
University of Maryland Global Campus — May 2026

Professional Focus: Cloud Engineering | AWS Cloud Security | Cybersecurity

GitHub: https://github.com/sokajnr

This project is part of my ongoing effort to build practical, job-ready experience in cloud engineering and cybersecurity through hands-on AWS implementation.

Documentation updated November 20, 2026. Preliminary IAM simulations completed; bucket-specific and runtime validation remain pending.
