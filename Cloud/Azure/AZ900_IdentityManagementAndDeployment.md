# Microsoft Azure Fundamentals (AZ-900): Identity, Deployment, and Management
  - Azure AD: Microsoft Entra ID: the Identity and Access management service in all Azure accounts
  - Azure resources, including Azure Resource Manager

## 2. Azure Identity, Authentication and Authorization

### Identity, Authentication and Authorization
  - Identity: who/what, a person/object claim to be --> "who/what I am"
  - Authentication: validating an identity claim --> "proof of who/what I am"
  - Authorization: what an identity can do
  - In Azure, "Identity" is a **unique identifier**, where "Authentication" is to **prove** that identity, while "Authorization" is about the **scopes** of that identity

### Azure Active Directory (Azure AD) --> re-brand as Entra ID
  - Ms Entra: a product **family** include Azure AD (i.e. Entra ID), Permissions Management and Verified ID
  - Azure Active Directory (e.g. Azure AD == Entra ID) vs Active Directory (AD): 
    + Entra ID: uses modern architecture, protocols, and methods (e.g. MFA), and has a cloud‑native design.
    + AD: ses legacy architecture, protocols, and methods, and is on‑premise/data center in design
  - Entra ID: every Azure account has an Entra ID instance, with at least one user, created with the initial Entra ID instance
  - EntrA ID root is Tenant --> represents the organization, assigned at signed up
    + Tenant is distinct & completely separate from other tenant
    + Each user an be a a member/guest of up to 500 tenants
    + A tenant can have multiple subscriptions --> represent resources that are billed together
      - If a subscription isn't paid --> resources & services associated with the subscription stop
  - Entra ID allows an on‑premises AD to connect to it, which then allows for those identities/users to use Azure resources

### Zero Trust Concepts
  - Classic trust model: by location (i.e. within a private network), rather than identity --> not suitable with mobile devices, nor it's fine-grain enough
  - Zero Trust model: trusted by identity --> all users assumed untrustworthy unless proven otherwise

### Multi-Factor Authentication
  - MFA: based on combination of mroe than 1 thing: something you know, you have, or something you're.
    + Username/password: something you know
    + Authenticate code: something you have (i.e. authenticate apps, smartphone,etc.)

### Conditional access
  - Policy that specified certain conditions --> grant/block access
    + Conditions: users/groups, application, location (i.e. IP), devices
    + Access decisions: grant/block, MFA requirement
  - MFA is often implemented with Conditional Access as further security

### Password-less authentication
  - Multi-Factor Authentication (MFA): more secure but **less convenient** (slower, more steps) --> password-less authentication: one possible solution
  - Replace password with 
    + something you have (phone/key fob)
    + something you know/are (on device): finger-print, face unlock, PIN
  - Some examples:
    + Microsoft Authenticator App: authenticate in app with biometrics/PIN
    + Windows Hello: face recognition
    + FIDO2 security key: hardware key that need to plug into the computer, may even have a finger-print on it

### External Guest Access
  - External Guest acess: enables security outside of your oganisational boundaries --> visibility of external guest activity within your organisational ID borders
  - For external access: Entra External ID for partners (Azuare AD B2B) and Entra External ID for customers (Azure AD B2C)
    + ID for partners (B2B): invite users to create new ID with us, based on their existing ID from external provider --> will authenticate by us, to access our resources
    + ID for customers (B2C): authenticated by 3rd party, to access our "external-facing" applications --> still need to assign access rights for that entity

### Azure Active Directory Domain Services
  - Legacy apps: 
    + Unable to use modern authentication protocols (e.g. OAuth 2.0)
    + Require traditional AD management/protocols (e.g. Group policy, LDAP, NTLM, Kerberos)
  - Azure AD Domain Service for legacy apps --> migrating or integrating legacy apps
    + Managed service: no need for OS config/management
    + One-way sync. from Entra ID to Azure AD
    + Behind the scenes: two Windows domain controllers for high-availability

### Managing access with Role-Based Access Control (RBAC)
  - Role-based access control: control access based on role --> two type: built-in & custom
    + Built-In: general use cases, for variation of Owner, contributor & reader
    + Custom: Fine-grained, specific use cases --> necessary permissions, for specific users of specific resources
  - Inheritance --> propagates permissions in a hierarchy
    + ability to "inherit" permissions from other roles
    + ability for entity to "inherit" permissions from roles it belongs to
  - Best practices:
    + Least privilege
    + Use of roles (especially built-in role whenever possible)
    + Role segregation: fine grain permission --> avoid combined roles with conflict permissions
    + Limit scope to avoid permission sprawl
    + Review, audit & document

### Defense in Depth
  - At identity level --> user identity is the data to protect
    + Defense in Depth: conditional access (e.g. physical limitation), MFA, password

## 3. Management and Deployment Tools
  - Azure Resource Manager (ARM): for create, update and delete of resources in an Azure account
    + provides consistent & convenience control of Azure resources
    + can access by portal, CLI or SDKs
  -  Azure Portal: web-based, visual interface for interacting with Azure
    + visual representation of resources --> for deployment, monitoring & alert, cost estimation & billing
    + Could create dashboards for vital information about important resources on log in
  - Azure portal global search: for resources & services, and documentation
  - Azure CLI: platform agnostic
    + can be run through Azure cloud shell (in Azure portal) or in local terminal (Azure CLI has to be installed)