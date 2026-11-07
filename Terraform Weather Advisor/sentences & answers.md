# Part A 

Why the -/+ plan is the one to be careful about

The -/+ means that terraform will destroy the resource and create a new one. So for a DynamoDB table, every item in it would be lost. The plan warns you before it happens, which is why you read it before applying.

## Steps 1-3

Sentence comparing it to running the bash script twice

The bash script only checked whether a resource with that name existed and then printed “already exists, skipping.” It never compared its settings to what the code says. Terraform compares every attribute in my files with the state and with AWS, so when it tells me “No changes,” it means that everything actually matches.

Terraform graph:

|                                 | lab06 bash | lab07 terraform |
|---------------------------------|------------|-----------------|
| lines to create stack           | 792        | 410             |
| lines to delete stack           | 197        | 0               |
| steps you had to order by hand  | Many       | 0               |
| helper functions                | Many       | 0               |
| retry or wait functions         | Many       | 0               |


## Steps 4-5

Write down the difference from lab06's ensure_target_group , which found the group already existing and said "skipping" without checking that the health check was still right.

The ensure_target_group only checked that a group with that name existed, so it would have said “skipping” even with the /nope health check. Terraform compared the real health check path to my code, showed the ~ change, and fixed the drift on apply.

## Steps 6-7

Write one sentence on what an attacker who takes over the advisor tier gets, and what they do not get.

An attacker who takes over the advisor tier can read the Gemini key in the Parameter and they could use it to burn my quota or to incur cost. They can’t read or write the DynamoDB table since it is implicitDeny, so they don't have access to the weather history and other things from AWS.

Write two sentences on what the next terraform apply would do to your key, and what would then be in the state. (remove ignore_changes)

The next terraform apply would see that the stored value differs from not-set-yet and overwrite my actual key with the placeholder, so the advisor would silently fall back. Terraform would also read the real value back and it would put the key in terraform.tfstate.

## Step 8

Write down one problem with a design where users never see an error and the only evidence is a log line that nobody reads.

The problem where users would never see an error is that it can feed them bad or incorect informaiton without them knowing. It can also go on for days since since there is only a log line as evidence that no one usually reads.

## Step 9

Lambda table: 

| Advisor by EC2 (this lab)               | Advisor by Lambda                               | Effort moved to |
|-----------------------------------------|-------------------------------------------------|-----------------|
| Launch template and user-data           | Lambda should run the code for me               | Lambda          |
| Auto Scaling Group and instance refresh | Lambda should scale automatically               | Lambda          |
| App security group rule for port 3000   | Might need a security group for outbound access | Lambda          |
| Instance profile and role               | Lambda execution role                           | Terraform       |
| Node.js install and systemd service     | Lambda provides runtime                         | Lambda          |
| Health check and grace period           | ALB still needs to know if Lambda is healthy    | Lambda or ALB   |

1. List every resource in compute.tf , iam.tf , network.tf and alb.tf that exists only because the advisor runs on an EC2 instance.

- launch template, user-data template, and local.user_data
- Auto Scaling Group
- iam instance profile and the EC2 trust policy
- aws_security_group
- AMI, VPC 
- key_name, ssh_cidr and instance_type variables

2. List what would stay (the parameter, the policy, the listener, the load balancer, and so on).

- ALB and its security group
- listener
- target group
- SSM parameter
- IAM role
- gemini_model

3. List what would be new (a function resource, a permission that lets the load balancer call it, and the work of changing the code to handle a Lambda event).

- aws_lambda_function
- aws_lambda_permission, so the ALB can invoke it
- A code change from express to an ALB-event handler

4. For each of your three lists, say whether Terraform or Lambda removed the effort. What did you gain, and what new problem did you take on (think: cold starts, time limits, how you would test it)?

Lambda removed:
- servers, OS patching, scaling config, etc.

Terraform removed:
- waiting, delete script

Gain:
- no idle cost and machine maintenance 

New problems:
- cold boot
- no health check unless we bring in ALB
- latency
- code rewrite

---

# Short answers

How Terraform is like AWS Lambda and where the comparison stops

Both terraform and lambda let me focus on the code and config while the cloud can handle the servers, scaling, dependencies, etc. The difference is that lambda runs the code then it stops. Terraform runs once to build things and then it remmbers what it built. 


What Terraform did not make easier

It didn't make my application shorter since there are more files needed to make it work compared to the bash scripts.

---

# My cost estimate: 

- Start:   Sat Nov 7 02:49:03 UTC 2026
- Destroy: Sat Nov 7 04:25:02 UTC 2026
- Runtime: 1 hour 36 minutes (1.6 hours)
- Rate:    ~$0.07 per hour
- Cost:    1.60 x $0.07 = $0.11

Note:
(The screenshot of 'delete verification cmds' shows left over security groups. The parent groups are deleted. This does not incur cost.)