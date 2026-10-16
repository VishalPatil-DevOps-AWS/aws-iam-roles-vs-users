
<div align="center">

<h1>☁️ AWS IAM Roles vs IAM Users</h1>

<h3>🔐 Secure AWS Access Management | Hands-on DevOps Project</h3>

<p>
  Learn how to secure AWS workloads using IAM Roles,
  temporary credentials, cross-account access,
  and GitHub Actions OIDC.
</p>

<p>
  <img src="https://img.shields.io/badge/AWS-IAM-FF9900?style=for-the-badge" alt="AWS IAM">
  <img src="https://img.shields.io/badge/AWS_CLI-v2-232F3E?style=for-the-badge" alt="AWS CLI">
  <img src="https://img.shields.io/badge/Bash-Scripts-4EAA25?style=for-the-badge" alt="Bash">
  <img src="https://img.shields.io/badge/GitHub_Actions-OIDC-2088FF?style=for-the-badge" alt="GitHub Actions">
  <img src="https://img.shields.io/badge/License-MIT-blue?style=for-the-badge" alt="MIT License">
</p>

<p>
  <a href="#-project-overview">Overview</a> •
  <a href="#-architecture">Architecture</a> •
  <a href="#-iam-user-vs-iam-role">Comparison</a> •
  <a href="#-quick-start">Quick Start</a> •
  <a href="#-security-best-practices">Security</a>
</p>

<p>
  <b>Built by Vishal Patil | DevOps & AWS Cloud</b>
</p>

</div>

<hr>

<h2>📌 Project Overview</h2>

<p>
This hands-on AWS project demonstrates how to use
IAM Roles instead of long-lived IAM User access keys
to securely access AWS resources.
</p>

<p><b>Project Objectives:</b></p>

<ul>
  <li>Configure EC2 access to Amazon S3 using an IAM Role.</li>
  <li>Understand IAM trust policies and permission policies.</li>
  <li>Use AWS STS temporary credentials.</li>
  <li>Implement secure cross-account access.</li>
  <li>Authenticate GitHub Actions to AWS using OIDC.</li>
  <li>Apply least-privilege security principles.</li>
</ul>

<hr>

<h2>🏗 Architecture</h2>

<div align="center">

<h3>AWS IAM Role-Based Access Architecture</h3>

<table>
<tr>
  <th>🖥️ Amazon EC2</th>
  <th>🔐 IAM Role</th>
  <th>🔑 AWS STS</th>
  <th>🪣 Amazon S3</th>
</tr>
<tr>
  <td align="center">
    Application<br>
    AWS CLI / SDK
  </td>
  <td align="center">
    Instance Profile<br>
    IAM Permissions
  </td>
  <td align="center">
    Temporary<br>
    Credentials
  </td>
  <td align="center">
    Read-only<br>
    Access
  </td>
</tr>
<tr>
  <td align="center" colspan="4">
    EC2 → IAM Role → AWS STS → Amazon S3
  </td>
</tr>
</table>

<p>
  <b>Secure Access • Temporary Credentials • No Hardcoded Keys</b>
</p>

</div>

<h3>How It Works</h3>

<ol>
  <li>An IAM Role is created with an EC2 trust policy.</li>
  <li>The role is added to an instance profile.</li>
  <li>The instance profile is attached to EC2.</li>
  <li>EC2 obtains temporary role credentials through its metadata service.</li>
  <li>The AWS CLI or SDK uses the credentials to access authorized S3 resources.</li>
</ol>

<hr>

<h2>🔐 IAM User vs IAM Role</h2>

<table>
<thead>
<tr>
  <th>Feature</th>
  <th>IAM User</th>
  <th>IAM Role</th>
</tr>
</thead>
<tbody>
<tr>
  <td><b>Credentials</b></td>
  <td>Long-term credentials</td>
  <td>Temporary credentials</td>
</tr>
<tr>
  <td><b>Identity</b></td>
  <td>Persistent IAM identity</td>
  <td>Assumable IAM identity</td>
</tr>
<tr>
  <td><b>Use Cases</b></td>
  <td>Legacy or exceptional access</td>
  <td>EC2, Lambda, CI/CD, federation</td>
</tr>
<tr>
  <td><b>Rotation</b></td>
  <td>Access keys require management</td>
  <td>Session credentials expire automatically</td>
</tr>
<tr>
  <td><b>Security</b></td>
  <td>Long-lived key exposure risk</td>
  <td>Reduced long-lived secret exposure</td>
</tr>
<tr>
  <td><b>Best Practice</b></td>
  <td>Prefer Identity Center for humans</td>
  <td>Prefer roles for workloads</td>
</tr>
</tbody>
</table>

<blockquote>
  <b>Security Best Practice:</b>
  Use IAM Roles for applications and AWS services instead of
  storing permanent access keys in application code.
</blockquote>

<hr>

<h2>🧰 Prerequisites</h2>

<ul>
  <li>AWS account with appropriate IAM permissions</li>
  <li>AWS CLI v2 installed and configured</li>
  <li>Amazon EC2 instance</li>
  <li>Bash terminal (Linux, macOS or WSL)</li>
  <li>Git installed</li>
</ul>

<hr>

<h2>🚀 Quick Start</h2>

<h3>Step 1: Clone Repository</h3>

```bash
git clone https://github.com/VishalPatil-DevOps-AWS/aws-iam-roles-vs-users.git

cd aws-iam-roles-vs-users/scripts
```

<h3>Step 2: Create IAM Role</h3>

```bash
./01-create-role.sh
```

<p>
Creates an IAM Role with an EC2 trust policy
and attaches AmazonS3ReadOnlyAccess.
</p>

<h3>Step 3: Create Instance Profile</h3>

```bash
./02-create-instance-profile.sh
```

<p>
Creates an instance profile and associates the IAM Role.
</p>

<h3>Step 4: Attach Role to EC2</h3>

<p>
AWS Console → EC2 → Instances → Select Instance →
Actions → Security → Modify IAM Role
</p>

<p>Select the instance profile and save.</p>

<h3>Step 5: Verify IAM Role</h3>

<p>Run this command on the EC2 instance:</p>

```bash
aws sts get-caller-identity
```

<p><b>Expected Output:</b></p>

```json
{
  "UserId": "AROAEXAMPLE:i-0123456789abcdef0",
  "Account": "123456789012",
  "Arn": "arn:aws:sts::123456789012:assumed-role/ec2-s3-readonly/i-0123456789abcdef0"
}
```

<p>
The <code>assumed-role</code> ARN indicates
that the AWS request is using an assumed IAM Role session.
</p>

<hr>

<h2>🌐 Cross-Account Access</h2>

<p>
IAM Roles allow an authorized principal in Account A
to assume a role in Account B.
</p>

<table>
<tr>
  <th>Account A</th>
  <th>Access Method</th>
  <th>Account B</th>
</tr>
<tr>
  <td align="center">IAM Principal</td>
  <td align="center">AWS STS AssumeRole →</td>
  <td align="center">IAM Role</td>
</tr>
</table>

<p><b>Example:</b></p>

```bash
./scripts/assume-cross-account-role.sh \
  <ACCOUNT_B_ID> prod-readonly my-session
```

<p>
The role trust policy must authorize the Account A principal,
and that principal must have permission to call
<code>sts:AssumeRole</code>.
</p>

<hr>

<h2>⚙️ GitHub Actions with OIDC</h2>

<p>
GitHub Actions can authenticate to AWS using
OpenID Connect without storing long-lived AWS access keys.
</p>

<h3>OIDC Workflow</h3>

<table>
<tr>
  <th>GitHub Actions</th>
  <th>OIDC Token</th>
  <th>AWS IAM Role</th>
</tr>
<tr>
  <td align="center">CI/CD Workflow</td>
  <td align="center">Federated Authentication</td>
  <td align="center">Temporary AWS Access</td>
</tr>
</table>

<h3>Setup Steps</h3>

<ol>
  <li>Create an AWS IAM OIDC identity provider.</li>
  <li>Use <code>token.actions.githubusercontent.com</code>.</li>
  <li>Create an IAM Role with an OIDC trust policy.</li>
  <li>Restrict the <code>aud</code> and <code>sub</code> conditions.</li>
  <li>Configure GitHub Actions with <code>id-token: write</code>.</li>
  <li>Use the AWS credentials action to assume the role.</li>
</ol>

<p><b>Example GitHub Actions Workflow:</b></p>

```yaml
name: AWS OIDC Authentication

on:
  workflow_dispatch:

permissions:
  id-token: write
  contents: read

jobs:
  aws-auth:
    runs-on: ubuntu-latest

    steps:
      - name: Configure AWS Credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: arn:aws:iam::<ACCOUNT_ID>:role/github-oidc-role
          aws-region: eu-north-1

      - name: Verify AWS Identity
        run: aws sts get-caller-identity
```

<p>
Replace <code>&lt;ACCOUNT_ID&gt;</code> with your AWS account ID
and configure the matching role and trust policy before running.
For production, pin GitHub Actions to reviewed commit SHAs.
</p>

<hr>

<h2>📁 Repository Structure</h2>

```text
aws-iam-roles-vs-users/
|
+-- assets/
|   +-- banner.svg
|   +-- architecture.svg
|
+-- docs/
|   +-- troubleshooting.md
|
+-- examples/
|   +-- github-actions-oidc.yml
|
+-- policies/
|   +-- trust-policy-ec2.json
|   +-- trust-policy-cross-account.json
|   +-- trust-policy-github-oidc.json
|
+-- scripts/
|   +-- config.sh
|   +-- 01-create-role.sh
|   +-- 02-create-instance-profile.sh
|   +-- 03-verify.sh
|   +-- assume-cross-account-role.sh
|   +-- cleanup.sh
|
+-- LICENSE
+-- README.md
```

<hr>

<h2>🛡 Security Best Practices</h2>

<table>
<tr>
  <th>Security Practice</th>
  <th>Recommendation</th>
</tr>
<tr>
  <td>🔑 Access Keys</td>
  <td>Never hardcode or commit AWS credentials.</td>
</tr>
<tr>
  <td>🔐 IAM Roles</td>
  <td>Prefer temporary credentials for workloads.</td>
</tr>
<tr>
  <td>🛡 Least Privilege</td>
  <td>Grant only required actions and resources.</td>
</tr>
<tr>
  <td>🔒 MFA</td>
  <td>Enable MFA for root and human access.</td>
</tr>
<tr>
  <td>📋 Monitoring</td>
  <td>Audit AWS activity using CloudTrail.</td>
</tr>
<tr>
  <td>⚙️ CI/CD</td>
  <td>Use OIDC instead of stored AWS keys.</td>
</tr>
</table>

<hr>

<h2>🧹 Cleanup</h2>

<p>
Detach the IAM Role from EC2 before deleting
the instance profile and role.
</p>

```bash
./scripts/cleanup.sh
```

<blockquote>
  <b>Cost Reminder:</b>
  IAM has no additional charge, but EC2 instances and
  other AWS resources may incur charges.
  Terminate unused resources after completing the lab.
</blockquote>

<hr>

<h2>👤 Author</h2>

<div align="center">

<h3>Vishal Patil</h3>

<p><b>DevOps & AWS Cloud Enthusiast</b></p>

<p>
Passionate about cloud computing, infrastructure automation,
AWS security and modern DevOps practices.
</p>

<p>
  <a href="https://github.com/VishalPatil-DevOps-AWS">
    <img src="https://img.shields.io/badge/GitHub-Profile-181717?style=for-the-badge" alt="GitHub">
  </a>
  <a href="https://www.linkedin.com/in/vishalpatil-devops-aws/">
    <img src="https://img.shields.io/badge/LinkedIn-Connect-0A66C2?style=for-the-badge" alt="LinkedIn">
  </a>
  <a href="https://dev.to/vishal_patil_aws">
    <img src="https://img.shields.io/badge/Dev.to-Articles-111111?style=for-the-badge" alt="Dev.to">
  </a>
</p>

<hr>

<h3>⭐ Support This Project</h3>

<p>
If you found this project useful,
consider giving the repository a star!
</p>

<p>Made with ❤️ for AWS & DevOps Learning</p>

<p><b>MIT License | © 2026 Vishal Patil</b></p>

</div>
