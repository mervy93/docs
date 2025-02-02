---
title: Configuring user provisioning for your enterprise
shortTitle: Configure user provisioning
intro: 'You can configure System for Cross-domain Identity Management (SCIM) for your enterprise, which automatically provisions user accounts on {% data variables.location.product_location %} when you assign the application for {% data variables.location.product_location %} to a user on your identity provider (IdP).'
permissions: 'Enterprise owners can configure user provisioning for an enterprise on {% data variables.product.product_name %}.'
versions:
  ghae: '*'
type: how_to
topics:
  - Accounts
  - Authentication
  - Enterprise
  - Identity
  - SSO
redirect_from:
  - /admin/authentication/configuring-user-provisioning-for-your-enterprise
  - /admin/identity-and-access-management/managing-iam-for-your-enterprise/configuring-user-provisioning-for-your-enterprise
---
## About user provisioning for your enterprise

{% data reusables.saml.ae-uses-saml-sso %} For more information, see "[Configuring SAML single sign-on for your enterprise](/admin/authentication/configuring-saml-single-sign-on-for-your-enterprise)."

You can configure user provisioning with SCIM to automatically create or suspend user accounts and grant access for {% data variables.product.product_name %} when you assign or unassign the application on your IdP. For more information about SCIM, see [System for Cross-domain Identity Management: Protocol (RFC 7644)](https://tools.ietf.org/html/rfc7644) on the IETF website.

If you do not configure user provisioning with SCIM, your IdP will not communicate with {% data variables.product.product_name %} automatically when you assign or unassign the application to a user. Without SCIM, {% data variables.product.product_name %} creates a user account using SAML Just-in-Time (JIT) provisioning the first time someone navigates to {% data variables.product.product_name %} and signs in by authenticating through your IdP.

Configuring provisioning allows your IdP to communicate with {% data variables.location.product_location %} when you assign or unassign the application for {% data variables.product.product_name %} to a user on your IdP. When you assign the application, your IdP will prompt {% data variables.location.product_location %} to create an account and send an onboarding email to the user. When you unassign the application, your IdP will communicate with {% data variables.product.product_name %} to invalidate any SAML sessions and disable the member's account.

To configure provisioning for your enterprise, you must enable provisioning on {% data variables.product.product_name %}, then install and configure a provisioning application on your IdP.

The provisioning application on your IdP communicates with {% data variables.product.product_name %} via our SCIM API for enterprises. For more information, see "[GitHub Enterprise administration](/rest/reference/enterprise-admin#scim)" in the {% data variables.product.prodname_dotcom %} REST API documentation.

## Supported identity providers

The following IdPs are supported for SSO with {% data variables.product.prodname_ghe_managed %}:

{% data reusables.saml.okta-ae-sso-beta %}

{% data reusables.github-ae.saml-idp-table %}

For IdPs that support team mapping, you can assign or unassign the application for {% data variables.product.product_name %} to groups of users in your IdP. These groups are then available to organization owners and team maintainers in {% data variables.location.product_location %} to map to {% data variables.product.product_name %} teams. For more information, see "[Mapping Okta groups to teams](/admin/authentication/configuring-authentication-and-provisioning-with-your-identity-provider/mapping-okta-groups-to-teams)."

## Prerequisites

To automatically provision and deprovision access to {% data variables.location.product_location %} from your IdP, you must first configure SAML SSO when you initialize {% data variables.product.product_name %}. For more information, see "[Initializing {% data variables.product.prodname_ghe_managed %}](/admin/configuration/initializing-github-ae)."

You must have administrative access on your IdP to configure the application for user provisioning for {% data variables.product.product_name %}.

## Enabling user provisioning for your enterprise

1. While signed into {% data variables.location.product_location %} as an enterprise owner, create a personal access token with **admin:enterprise** scope. For more information, see "[Creating a personal access token](/github/authenticating-to-github/creating-a-personal-access-token)."
  {% note %}

  **Notes**:
    - To create the personal access token, we recommend using the account for the first enterprise owner that you created during initialization. For more information, see "[Initializing {% data variables.product.prodname_ghe_managed %}](/admin/configuration/initializing-github-ae)."
    - You'll need this personal access token to configure the application for SCIM on your IdP. Store the token securely in a password manager until you need the token again later in these instructions.

  {% endnote %}
  {% warning %}

  **Warning**: If the user account for the enterprise owner who creates the personal access token is deactivated or deprovisioned, your IdP will no longer provision and deprovision user accounts for your enterprise automatically. Another enterprise owner must create a new personal access token and reconfigure provisioning on the IdP.

  {% endwarning %}
{% data reusables.enterprise-accounts.access-enterprise %}
{% data reusables.enterprise-accounts.settings-tab %}
{% data reusables.enterprise-accounts.security-tab %}
1. Under "SCIM User Provisioning", select **Require SCIM user provisioning**.
  ![Checkbox for "Require SCIM user provisioning" within enterprise security settings](/assets/images/help/enterprises/settings-require-scim-user-provisioning.png)
1. Click **Save**.
  ![Save button under "Require SCIM user provisioning" within enterprise security settings](/assets/images/help/enterprises/settings-scim-save.png)
1. Configure user provisioning in the application for {% data variables.product.product_name %} on your IdP.

  The following IdPs provide documentation about configuring provisioning for {% data variables.product.product_name %}. If your IdP isn't listed, please contact your IdP to request support for {% data variables.product.product_name %}.

  | IdP | More information |
  | :- | :- |
  | Azure AD | [Tutorial: Configure {% data variables.product.prodname_ghe_managed %} for automatic user provisioning](https://docs.microsoft.com/azure/active-directory/saas-apps/github-ae-provisioning-tutorial) in the Microsoft Docs. To configure Azure AD for {% data variables.product.prodname_ghe_managed %}, see "[Configuring authentication and provisioning for your enterprise using Azure AD](/admin/authentication/configuring-authentication-and-provisioning-with-your-identity-provider/configuring-authentication-and-provisioning-for-your-enterprise-using-azure-ad)."|
| Okta | (beta) To configure Okta for {% data variables.product.prodname_ghe_managed %}, see "[Configuring authentication and provisioning for your enterprise using Okta](/admin/authentication/configuring-authentication-and-provisioning-with-your-identity-provider/configuring-authentication-and-provisioning-for-your-enterprise-using-okta)."|

  The application on your IdP requires two values to provision or deprovision user accounts on {% data variables.location.product_location %}.

  | Value | Other names | Description | Example |
  | :- | :- | :- | :- |
  | URL | Tenant URL | URL to the SCIM provisioning API for your enterprise on {% data variables.product.prodname_ghe_managed %} | <nobr><code>{% data variables.product.api_url_pre %}/scim/v2</nobr></code> |
  | Shared secret | Personal access token, secret token | Token for application on your IdP to perform provisioning tasks on behalf of an enterprise owner | Personal access token you created in step 1 |
