# Application Gateway
This module will create an application gateway.

You can optionally configure the following:
- Web Application Firewall (WAF)
- Application Gateway Firewall Policy
- Key vault integration with a managed identity for certificate retrieval
- Diagnostic logs and resource lock


## Usage

### Example 1 - Application Gateway with diagnostic logs and resource lock

```bicep
param deploymentName string = 'appgw${utcNow()}'

module appGateway 'applicationgateway.bicep' = {
  name: deploymentName
  params: {
    applicationGatewayName: 'MyApplicationGateway'
    sku: 'Standard_v2'
    tier: 'Standard_v2'
    zoneRedundant: true
    publicIpAddressName: 'MyPublicIpAddress'
    vNetResourceGroup: 'MyVnetResourceGroup'
    vNetName: 'MyVnetName'
    subnetName: 'MySubnetName'
    frontEndPorts: [
      {
        name: 'port_80'
        port: 80
      }
    ]
    httpListeners: [
      {
        name: 'MyHttpListener'
        protocol: 'Http'
        port: 80
        frontEndPort: 'port_80'
      }
    ]
    backendAddressPools: [
      {
        name: 'MyBackendPool'
        backendAddresses: [
          {
            ipAddress: '10.1.2.3'
          }
        ]
      }
    ]
    backendHttpSettings: [
      {
        name: 'MyBackendHttpSetting'
        port: 80
        protocol: 'Http'
        cookieBasedAffinity: 'Enabled'
        affinityCookieName: 'MyCookieAffinityName'
        requestTimeout: 300
        connectionDraining: {
          drainTimeoutInSec: 60
          enabled: true
        }
      }
    ]
    rules: [
      {
        name: 'MyRuleName'
        ruleType: 'Basic'
        listener: 'MyHttpListener'
        backendPool: 'MyBackendPool'
        backendHttpSettings: 'MyBackendHttpSetting'
      }
    ]
    enableDeleteLock: true
    enableDiagnostics: true
    logAnalyticsWorkspaceId: 'MyLogAnalyticsWorkspaceResourceId'
    diagnosticStorageAccountId: 'MyStorageAccountResourceId'
  }
}
```
### Example 2 - Application Gateway with WAF

```bicep
param deploymentName string = 'appgw${utcNow()}'

module appGateway 'applicationgateway.bicep' = {
  name: deploymentName
  params: {
    applicationGatewayName: 'MyApplicationGateway'
    sku: 'WAF_v2'
    tier: 'WAF_v2'
    zoneRedundant: true
    enableWebApplicationFirewall: true
    firewallPolicyName: 'MyFirewallPolicyName'
    publicIpAddressName: 'MyPublicIpAddress'
    vNetResourceGroup: 'MyVnetResourceGroup'
    vNetName: 'MyVnetName'
    subnetName: 'MySubnetName'
    frontEndPorts: [
      {
        name: 'port_80'
        port: 80
      }
    ]
    httpListeners: [
      {
        name: 'MyHttpListener'
        protocol: 'Http'
        port: 80
        frontEndPort: 'port_80'
      }
    ]
    backendAddressPools: [
      {
        name: 'MyBackendPool'
        backendAddresses: [
          {
            ipAddress: '10.1.2.3'
          }
        ]
      }
    ]
    backendHttpSettings: [
      {
        name: 'MyBackendHttpSetting'
        port: 80
        protocol: 'Http'
        cookieBasedAffinity: 'Enabled'
        affinityCookieName: 'MyCookieAffinityName'
        requestTimeout: 300
        connectionDraining: {
          drainTimeoutInSec: 60
          enabled: true
        }
      }
    ]
    rules: [
      {
        name: 'MyRuleName'
        ruleType: 'Basic'
        listener: 'MyHttpListener'
        backendPool: 'MyBackendPool'
        backendHttpSettings: 'MyBackendHttpSetting'
      }
    ]
  }
}
```

### Example 3 - Application Gateway with custom probe

```bicep
param deploymentName string = 'appgw${utcNow()}'

module appGateway 'applicationgateway.bicep' = {
  name: deploymentName
  params: {
    applicationGatewayName: 'MyApplicationGateway'
    sku: 'WAF_v2'
    tier: 'WAF_v2'
    enableWebApplicationFirewall: true
    firewallPolicyName: 'MyFirewallPolicyName'
    publicIpAddressName: 'MyPublicIpAddress'
    vNetResourceGroup: 'MyVnetResourceGroup'
    vNetName: 'MyVnetName'
    subnetName: 'MySubnetName'
    customProbes: [
      {
        name: 'MyCustomProbe'        
        protocol: 'Http'
        host: 'example.com.au'
        path: '/'
        interval: 30
        timeout: 10
        unhealthyThreshold: 3
        pickHostNameFromBackendHttpSettings: false
        minServers: 0
        match: {
          statusCodes: [
            '200-399'
          ]
        }        
      }
    ]
    frontEndPorts: [
      {
        name: 'port_80'
        port: 80
      }
    ]
    httpListeners: [
      {
        name: 'MyHttpListener'
        protocol: 'Http'
        port: 80
        frontEndPort: 'port_80'
      }
    ]
    backendAddressPools: [
      {
        name: 'MyBackendPool'
        backendAddresses: [
          {
            ipAddress: '10.1.2.3'
          }
        ]
      }
    ]
    backendHttpSettings: [
      {
        name: 'MyBackendHttpSetting'
        port: 80
        protocol: 'Http'
        cookieBasedAffinity: 'Enabled'
        affinityCookieName: 'MyCookieAffinityName'
        requestTimeout: 300
        connectionDraining: {
          drainTimeoutInSec: 60
          enabled: true
        }
        probeName: 'MyCustomProbe'
      }
    ]
    rules: [
      {
        name: 'MyRuleName'
        ruleType: 'Basic'
        listener: 'MyHttpListener'
        backendPool: 'MyBackendPool'
        backendHttpSettings: 'MyBackendHttpSetting'
      }
    ]
  }
}
```

### Example 4 - Application Gateway with redirection rule

```bicep
param deploymentName string = 'appgw${utcNow()}'

module appGateway 'applicationgateway.bicep' = {
  name: deploymentName
  params: {
    applicationGatewayName: 'MyApplicationGateway'
    sku: 'WAF_v2'
    tier: 'WAF_v2'
    enableWebApplicationFirewall: true
    firewallPolicyName: 'MyFirewallPolicyName'
    publicIpAddressName: 'MyPublicIpAddress'
    vNetResourceGroup: 'MyVnetResourceGroup'
    vNetName: 'MyVnetName'
    subnetName: 'MySubnetName'
    redirectConfigurations: [
      {
        name: 'MyRedirectonRuleName'
        redirectType: 'Permanent'
        targetUrl: 'https://www.example.com.au'
        includePath: true
        includeQueryString: true
        requestRoutingRule: 'MyRuleName'
      }
    ]
    customProbes: [
      {
        name: 'MyCustomProbe'        
        protocol: 'Http'
        host: 'example.com.au'
        path: '/'
        interval: 30
        timeout: 10
        unhealthyThreshold: 3
        pickHostNameFromBackendHttpSettings: false
        minServers: 0
        match: {
          statusCodes: [
            '200-399'
          ]
        }        
      }
    ]
    frontEndPorts: [
      {
        name: 'port_80'
        port: 80
      }
    ]
    httpListeners: [
      {
        name: 'MyHttpListener'
        protocol: 'Http'
        port: 80
        frontEndPort: 'port_80'
      }
    ]
    rules: [
      {
        name: 'MyRuleName'
        ruleType: 'Basic'
        listener: 'MyHttpListener'
        redirectConfiguration: 'MyRedirectonRuleName'
      }
    ]
  }
}
```

### Example 5 - Application Gateway retrieving certificates from key vault

```bicep
param deploymentName string = 'appgw${utcNow()}'

module appGateway 'applicationgateway.bicep' = {
  name: deploymentName
  params: {
    applicationGatewayName: 'MyApplicationGateway'
    sku: 'WAF_v2'
    tier: 'WAF_v2'
    enableWebApplicationFirewall: true
    firewallPolicyName: 'MyFirewallPolicyName'
    publicIpAddressName: 'MyPublicIpAddress'
    vNetResourceGroup: 'MyVnetResourceGroup'
    vNetName: 'MyVnetName'
    subnetName: 'MySubnetName'
    managedIdentityResourceId: 'MyManagedIdentityResourceId'
    sslCertificates: [
      {
        name: 'MySslCertName'
        keyVaultResourceId: 'MyKeyVaultResourceId'
        secretName: 'MySecretName'
      }
    ]
    trustedRootCertificates: [
      {
        name: 'MyTrustedRootCertName'
        keyVaultResourceId: 'MyKeyVaultResourceId'
        secretName: 'MySecretName'
      }
    ]
    customProbes: [
      {
        name: 'MyCustomProbe'        
        protocol: 'Http'
        host: 'example.com.au'
        path: '/'
        interval: 30
        timeout: 10
        unhealthyThreshold: 3
        pickHostNameFromBackendHttpSettings: false
        minServers: 0
        match: {
          statusCodes: [
            '200-399'
          ]
        }        
      }
    ]
    frontEndPorts: [
      {
        name: 'port_80'
        port: 80
      }
      {
        name: 'port_443'
        port: 443
      }
    ]
    httpListeners: [
      {
        name: 'MyHttpListener'
        protocol: 'Http'
        port: 80
        frontEndPort: 'port_80'
      }
      {
        name: 'MyHttpsListener'
        protocol: 'Https'
        port: 443
        frontEndPort: 'port_443'
        sslCertificate: 'MySslCertName'
        hostNames: [
          'example.com.au'
        ]
        firewallPolicy: 'enabled'
      }
    ]
    backendAddressPools: [
      {
        name: 'MyBackendPool'
        backendAddresses: [
          {
            fqdn: 'example.com'
          }
        ]
      }
    ]
    backendHttpSettings: [
      {
        name: 'MyBackendHttpSetting'
        port: 80
        protocol: 'Http'
        cookieBasedAffinity: 'Enabled'
        affinityCookieName: 'MyCookieAffinityName'
        requestTimeout: 300
        connectionDraining: {
          drainTimeoutInSec: 60
          enabled: true
        }
        probeName: 'MyCustomProbe'
      }
      {
        name: 'MyBackendHttpsSetting'
        port: 443
        protocol: 'Https'
        cookieBasedAffinity: 'Disabled'
        requestTimeout: 300
        connectionDraining: {
          drainTimeoutInSec: 60
          enabled: true
        }
        trustedRootCertificate: 'MyTrustedRootCertName'
        hostName: 'ca.example.com'
      }
    ]
    rules: [
      {
        name: 'MyRuleName'
        ruleType: 'Basic'
        listener: 'MyHttpListener'
        backendPool: 'MyBackendPool'
        backendHttpSettings: 'MyBackendHttpSetting'
      }
    ]
  }
}
```