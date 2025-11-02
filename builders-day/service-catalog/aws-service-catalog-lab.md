# AWS Service Catalog Builder's Day - S3 Bucket Provisioning Lab
**AWS Certified Cloud Practitioner**  
*Hands-on lab with AWS Service Catalog, IAM roles, and self-service infrastructure*

---

## Prerequisites
- AWS Account with admin access
- All resources will be created in **us-west-2**
- Basic understanding of CloudFormation templates

## Lab Overview
You'll create a Service Catalog portfolio that allows end users to provision S3 buckets without direct AWS console access. You'll configure IAM roles, launch constraints, and test the self-service provisioning experience.

---

## Task 1: Download CloudFormation Template

### 1.1 Download Template
1. **Open browser and navigate to:**
   - URL: https://github.com/buildwithbrainyl/ccp/blob/main/builders-day/iac/simple-cloudformation-example.yaml
2. **Click "Raw" button** (top-right of file contents)
3. **Right-click → Save As**
4. **Save file as:** `s3-bucket-template.yaml`
5. **Remember the download location** (you'll upload this later)

---

## Task 2: Create IAM Role for Launch Constraint

### 2.1 Create Role
1. **Sign in to AWS Console**
2. **Search for "IAM"** and select **IAM**
3. **Ensure you're in us-west-2 region**
4. **In left menu: Roles**
5. **Click "Create role"**

**Trusted entity type:**
6. **Select "AWS service"**
7. **Use case: Service Catalog**
8. **Click "Next"**

**Attach permissions:**
9. **Search and select:** `AmazonS3FullAccess`
10. **Search and select:** `AWSCloudFormationFullAccess`
11. **Click "Next"**

**Name and review:**
12. **Role name:** `ServiceCatalogLaunchRole`
13. **Description:** `Launch constraint role for Service Catalog products`
14. **Click "Create role"**

---

## Task 3: Create End User with Service Catalog Permissions

### 3.1 Create IAM Policy
1. **In left menu: Policies**
2. **Click "Create policy"**
3. **Click "JSON" tab**
4. **Replace existing content with:**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "servicecatalog:ProvisionProduct",
        "servicecatalog:UpdateProvisionedProduct",
        "servicecatalog:TerminateProvisionedProduct"
      ],
      "Resource": "*"
    }
  ]
}
```
5. **Click "Next"**
6. **Policy name:** `ServiceCatalogEndUserPolicy`
7. **Click "Create policy"**

### 3.2 Create IAM User
1. **In left menu: Users**
2. **Click "Create user"**
3. **User name:** `ServiceCatalogEndUser`
4. **Check "Provide user access to the AWS Management Console"**
5. **Select "I want to create an IAM user"**
6. **Console password:** Select **"Custom password"**
7. **Enter password:** `TempPassword123!`
8. **Uncheck "Users must create a new password at next sign-in"**
9. **Click "Next"**

**Set permissions:**
10. **Select "Attach policies directly"**
11. **Search and select:** `ServiceCatalogEndUserPolicy`
12. **Search and select:** `AWSServiceCatalogEndUserReadOnlyAccess`
13. **Click "Next"**
14. **Click "Create user"**

---

## Task 4: Create Service Catalog Product

### 4.1 Navigate to Service Catalog
1. **Search for "Service Catalog"** and select **AWS Service Catalog**
2. **Ensure you're in us-west-2 region**

### 4.2 Create Product
1. **In left menu: Products**
2. **Click "Create product"**

**Product details:**
3. **Product name:** `S3-Bucket`
4. **Description:** `Self-service S3 bucket provisioning`
5. **Owner:** `IT Operations`
6. **Distributor:** Leave blank

**Version details:**
7. **Method:** Select **"Use a CloudFormation template"**
8. **Select template:** Choose **"Upload a template file"**
9. **Click "Choose file"** and select `s3-bucket-template.yaml`
10. **Version title:** `v1.0`

**Support details:**
11. **Email contact:** Enter any email (e.g., `support@example.com`)

12. **Click "Create product"**

---

## Task 5: Create Portfolio and Add Product

### 5.1 Create Portfolio
1. **In left menu: Portfolios**
2. **Click "Create portfolio"**

**Portfolio details:**
3. **Portfolio name:** `Self-Service-Infrastructure`
4. **Description:** `Self-service AWS resources for end users`
5. **Owner:** `IT Operations`
6. **Click "Create portfolio"**

### 5.2 Add Product to Portfolio
1. **You'll be on the portfolio details page**
2. **Click "Products" tab**
3. **Click "Add product"**
4. **Select `S3-Bucket` from the list**
5. **Click "Add product"**

### 5.3 Grant User Access
1. **Click "Access" tab**
2. **Click "Grant access"**
3. **Under "Users, groups, and roles":** Select **Users**
4. **Search and select:** `ServiceCatalogEndUser`
5. **Click "Grant access"**

### 5.4 Add Launch Constraint
1. **Click "Constraints" tab**
2. **Click "Create constraint"**
3. **Product:** Select `S3-Bucket`
4. **Constraint type:** Select **"Launch"**
5. **Click "Continue"**
6. **IAM role:** Select `ServiceCatalogLaunchRole`
7. **Click "Create"**

---

## Task 6: Test as End User

### 6.1 Sign In as End User
1. **Copy your AWS account ID** (click account dropdown in top-right)
2. **Open incognito/private browser window**
3. **Navigate to AWS Console**
4. **Sign in as IAM user:**
   - Account ID: (paste your account ID)
   - IAM user name: `ServiceCatalogEndUser`
   - Password: `TempPassword123!`

### 6.2 Provision Product
1. **Search for "Service Catalog"** and select **AWS Service Catalog**
2. **Ensure you're in us-west-2 region**
3. **Click on "Products" in left menu**
4. **Click on "S3-Bucket" product tile**
5. **Click "Launch product"**

**Configure product:**
6. **Provisioned product name:** `my-test-bucket`
7. **Version:** v1.0 (pre-selected)
8. **Click "Next"**

**Parameters:**
9. **BucketName:** `sc-test-bucket-<your-initials>-<random-number>` (e.g., `sc-test-bucket-ab-12345`)
10. **Click "Next"**
11. **Click "Launch product"**

### 6.3 Monitor Provisioning
1. **You'll be redirected to provisioned products page**
2. **Status will show "Under change"**
3. **Wait 1-2 minutes for status to change to "Available"**
4. **Click on the provisioned product name**
5. **View "Outputs" tab to see bucket name and ARN**

### 6.4 Verify in S3
1. **Search for "S3"** and select **S3**
2. **Verify your bucket exists in the list**

### 6.5 Terminate Product
1. **Go back to Service Catalog → Provisioned products**
2. **Select your provisioned product**
3. **Click "Actions" → "Terminate"**
4. **Click "Terminate" to confirm**
5. **Wait for status to show "Terminated"**

---

## Testing Summary
✅ **Task 1:** CloudFormation template downloaded  
✅ **Task 2:** Launch constraint role created with CloudFormation and S3 permissions  
✅ **Task 3:** End user created with Service Catalog provisioning permissions  
✅ **Task 4:** S3 bucket product created from CloudFormation template  
✅ **Task 5:** Portfolio created with product, user access, and launch constraint  
✅ **Task 6:** Successfully provisioned and terminated S3 bucket as end user  

---

## Clean Up

### Delete in Order:
1. **Service Catalog (as admin user):**
   - Navigate to Portfolios
   - Select `Self-Service-Infrastructure`
   - Constraints tab → Delete launch constraint
   - Products tab → Remove product
   - Delete portfolio
   - Navigate to Products
   - Delete `S3-Bucket` product

2. **IAM:**
   - Users → Delete `ServiceCatalogEndUser`
   - Policies → Delete `ServiceCatalogEndUserPolicy`
   - Roles → Delete `ServiceCatalogLaunchRole`

3. **S3:**
   - If bucket still exists, delete it from S3 console

---

## Troubleshooting

### Cannot Provision Product
- Verify launch constraint is added to portfolio
- Check user has both custom policy and read-only access policy
- Ensure portfolio access is granted to user

### Provisioning Fails
- Check bucket name is globally unique
- Verify ServiceCatalogLaunchRole has CloudFormation and S3 permissions
- Review CloudFormation stack events for errors

### User Cannot See Portfolio
- Confirm portfolio access is granted to ServiceCatalogEndUser
- Ensure user is in correct region (us-west-2)

---

## What You've Learned
- Created IAM roles with launch constraint permissions
- Built end-user policies for self-service provisioning
- Created Service Catalog products from CloudFormation templates
- Configured portfolios with access controls and launch constraints
- Tested self-service infrastructure provisioning as an end user

---

**Lab Complete! You've successfully set up AWS Service Catalog for self-service infrastructure provisioning!**

