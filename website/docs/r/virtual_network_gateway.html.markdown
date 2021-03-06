---
layout: "azurerm"
page_title: "Azure Resource Manager: azurerm_virtual_network_gateway"
sidebar_current: "docs-azurerm-resource-network-virtual-network-gateway-x"
description: |-
  Manages a virtual network gateway to establish secure, cross-premises connectivity.
---

# azurerm_virtual_network_gateway

Manages a Virtual Network Gateway to establish secure, cross-premises connectivity.

-> **Note:** Please be aware that provisioning a Virtual Network Gateway takes a long time (between 30 minutes and 1 hour)

## Example Usage

```hcl
resource "azurerm_resource_group" "test" {
  name     = "test"
  location = "West US"
}

resource "azurerm_virtual_network" "test" {
  name                = "test"
  location            = "${azurerm_resource_group.test.location}"
  resource_group_name = "${azurerm_resource_group.test.name}"
  address_space       = ["10.0.0.0/16"]
}

resource "azurerm_subnet" "test" {
  name                 = "GatewaySubnet"
  resource_group_name  = "${azurerm_resource_group.test.name}"
  virtual_network_name = "${azurerm_virtual_network.test.name}"
  address_prefix       = "10.0.1.0/24"
}

resource "azurerm_public_ip" "test" {
  name                = "test"
  location            = "${azurerm_resource_group.test.location}"
  resource_group_name = "${azurerm_resource_group.test.name}"

  allocation_method = "Dynamic"
}

resource "azurerm_virtual_network_gateway" "test" {
  name                = "test"
  location            = "${azurerm_resource_group.test.location}"
  resource_group_name = "${azurerm_resource_group.test.name}"

  type     = "Vpn"
  vpn_type = "RouteBased"

  active_active = false
  enable_bgp    = false
  sku           = "Basic"

  ip_configuration {
    name                          = "vnetGatewayConfig"
    public_ip_address_id          = "${azurerm_public_ip.test.id}"
    private_ip_address_allocation = "Dynamic"
    subnet_id                     = "${azurerm_subnet.test.id}"
  }

  vpn_client_configuration {
    address_space = ["10.2.0.0/24"]

    root_certificate {
      name = "DigiCert-Federated-ID-Root-CA"

      public_cert_data = <<EOF
MIIDuzCCAqOgAwIBAgIQCHTZWCM+IlfFIRXIvyKSrjANBgkqhkiG9w0BAQsFADBn
MQswCQYDVQQGEwJVUzEVMBMGA1UEChMMRGlnaUNlcnQgSW5jMRkwFwYDVQQLExB3
d3cuZGlnaWNlcnQuY29tMSYwJAYDVQQDEx1EaWdpQ2VydCBGZWRlcmF0ZWQgSUQg
Um9vdCBDQTAeFw0xMzAxMTUxMjAwMDBaFw0zMzAxMTUxMjAwMDBaMGcxCzAJBgNV
BAYTAlVTMRUwEwYDVQQKEwxEaWdpQ2VydCBJbmMxGTAXBgNVBAsTEHd3dy5kaWdp
Y2VydC5jb20xJjAkBgNVBAMTHURpZ2lDZXJ0IEZlZGVyYXRlZCBJRCBSb290IENB
MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAvAEB4pcCqnNNOWE6Ur5j
QPUH+1y1F9KdHTRSza6k5iDlXq1kGS1qAkuKtw9JsiNRrjltmFnzMZRBbX8Tlfl8
zAhBmb6dDduDGED01kBsTkgywYPxXVTKec0WxYEEF0oMn4wSYNl0lt2eJAKHXjNf
GTwiibdP8CUR2ghSM2sUTI8Nt1Omfc4SMHhGhYD64uJMbX98THQ/4LMGuYegou+d
GTiahfHtjn7AboSEknwAMJHCh5RlYZZ6B1O4QbKJ+34Q0eKgnI3X6Vc9u0zf6DH8
Dk+4zQDYRRTqTnVO3VT8jzqDlCRuNtq6YvryOWN74/dq8LQhUnXHvFyrsdMaE1X2
DwIDAQABo2MwYTAPBgNVHRMBAf8EBTADAQH/MA4GA1UdDwEB/wQEAwIBhjAdBgNV
HQ4EFgQUGRdkFnbGt1EWjKwbUne+5OaZvRYwHwYDVR0jBBgwFoAUGRdkFnbGt1EW
jKwbUne+5OaZvRYwDQYJKoZIhvcNAQELBQADggEBAHcqsHkrjpESqfuVTRiptJfP
9JbdtWqRTmOf6uJi2c8YVqI6XlKXsD8C1dUUaaHKLUJzvKiazibVuBwMIT84AyqR
QELn3e0BtgEymEygMU569b01ZPxoFSnNXc7qDZBDef8WfqAV/sxkTi8L9BkmFYfL
uGLOhRJOFprPdoDIUBB+tmCl3oDcBy3vnUeOEioz8zAkprcb3GHwHAK+vHmmfgcn
WsfMLH4JCLa/tRYL+Rw/N3ybCkDp00s0WUZ+AoDywSl0Q/ZEnNY0MsFiw6LyIdbq
M/s/1JRtO3bDSzD9TazRVzn2oBqzSa8VgIo5C1nOnoAKJTlsClJKvIhnRlaLQqk=
EOF
    }

    revoked_certificate {
      name       = "Verizon-Global-Root-CA"
      thumbprint = "912198EEF23DCAC40939312FEE97DD560BAE49B1"
    }
  }
}
```

## Argument Reference

The following arguments are supported:

* `name` - (Required) The name of the Virtual Network Gateway. Changing the name
    forces a new resource to be created.

* `resource_group_name` - (Required) The name of the resource group in which to
    create the Virtual Network Gateway. Changing the resource group name forces
    a new resource to be created.

* `location` - (Required) The location/region where the Virtual Network Gateway is
    located. Changing the location/region forces a new resource to be created.

* `type` - (Required) The type of the Virtual Network Gateway. Valid options are
    `Vpn` or `ExpressRoute`. Changing the type forces a new resource to be created.

* `vpn_type` - (Optional) The routing type of the Virtual Network Gateway. Valid
    options are `RouteBased` or `PolicyBased`. Defaults to `RouteBased`.

* `enable_bgp` - (Optional) If `true`, BGP (Border Gateway Protocol) will be enabled
    for this Virtual Network Gateway. Defaults to `false`.

* `active_active` - (Optional) If `true`, an active-active Virtual Network Gateway
    will be created. An active-active gateway requires a `HighPerformance` or an
    `UltraPerformance` sku. If `false`, an active-standby gateway will be created.
    Defaults to `false`.

* `default_local_network_gateway_id` -  (Optional) The ID of the local network gateway
    through which outbound Internet traffic from the virtual network in which the
    gateway is created will be routed (*forced tunneling*). Refer to the
    [Azure documentation on forced tunneling](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm).
    If not specified, forced tunneling is disabled.

* `sku` - (Required) Configuration of the size and capacity of the virtual network
    gateway. Valid options are `Basic`, `Standard`, `HighPerformance`, `UltraPerformance`,
    `ErGw1AZ`, `ErGw2AZ`, `ErGw3AZ`, `VpnGw1`, `VpnGw2` and `VpnGw3`
    and depend on the `type` and `vpn_type` arguments.
    A `PolicyBased` gateway only supports the `Basic` sku. Further, the `UltraPerformance`
    sku is only supported by an `ExpressRoute` gateway.

* `ip_configuration` (Required) One or two `ip_configuration` blocks documented below.
    An active-standby gateway requires exactly one `ip_configuration` block whereas
    an active-active gateway requires exactly two `ip_configuration` blocks.

* `vpn_client_configuration` (Optional) A `vpn_client_configuration` block which
    is documented below. In this block the Virtual Network Gateway can be configured
    to accept IPSec point-to-site connections.

* `tags` - (Optional) A mapping of tags to assign to the resource.

The `ip_configuration` block supports:

* `name` - (Optional) A user-defined name of the IP configuration. Defaults to
    `vnetGatewayConfig`.

* `private_ip_address_allocation` - (Optional) Defines how the private IP address
    of the gateways virtual interface is assigned. Valid options are `Static` or
    `Dynamic`. Defaults to `Dynamic`.

* `subnet_id` - (Required) The ID of the gateway subnet of a virtual network in
    which the virtual network gateway will be created. It is mandatory that
    the associated subnet is named `GatewaySubnet`. Therefore, each virtual
    network can contain at most a single Virtual Network Gateway.

* `public_ip_address_id` - (Optional) The ID of the public ip address to associate
    with the Virtual Network Gateway.

The `vpn_client_configuration` block supports:

* `address_space` - (Required) The address space out of which ip addresses for
    vpn clients will be taken. You can provide more than one address space, e.g.
    in CIDR notation.
* `root_certificate` - (Optional) One or more `root_certificate` blocks which are
    defined below. These root certificates are used to sign the client certificate
    used by the VPN clients to connect to the gateway.
    This setting is incompatible with the use of `radius_server_address` and `radius_server_secret`.

* `revoked_certificate` - (Optional) One or more `revoked_certificate` blocks which
    are defined below.
    This setting is incompatible with the use of `radius_server_address` and `radius_server_secret`.

* `radius_server_address` - (Optional) The address of the Radius server.
    This setting is incompatible with the use of `root_certificate` and `revoked_certificate`.

* `radius_server_secret` - (Optional) The secret used by the Radius server.
    This setting is incompatible with the use of `root_certificate` and `revoked_certificate`.

* `vpn_client_protocols` - (Optional) List of the protocols supported by the vpn client.
    The supported values are `SSTP`, `IkeV2` and `OpenVPN`.

-> **NOTE:** Support for `OpenVPN` as a Client Protocol is currently in Public Preview - [you can register for this Preview using this link](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-howto-openvpn).

The `bgp_settings` block supports:

* `asn` - (Optional) The Autonomous System Number (ASN) to use as part of the BGP.

* `peering_address` - (Optional) The BGP peer IP address of the virtual network
    gateway. This address is needed to configure the created gateway as a BGP Peer
    on the on-premises VPN devices. The IP address must be part of the subnet of
    the Virtual Network Gateway. Changing this forces a new resource to be created.

* `peer_weight` - (Optional) The weight added to routes which have been learned
    through BGP peering. Valid values can be between `0` and `100`.

The `root_certificate` block supports:

* `name` - (Required) A user-defined name of the root certificate.

* `public_cert_data` - (Required) The public certificate of the root certificate
    authority. The certificate must be provided in Base-64 encoded X.509 format
    (PEM). In particular, this argument *must not* include the
    `-----BEGIN CERTIFICATE-----` or `-----END CERTIFICATE-----` markers.

The `root_revoked_certificate` block supports:

* `name` - (Required) A user-defined name of the revoked certificate.

* `public_cert_data` - (Required) The SHA1 thumbprint of the certificate to be
    revoked.

## Attributes Reference

The following attributes are exported:

* `id` - The ID of the Virtual Network Gateway.

## Import

Virtual Network Gateways can be imported using the `resource id`, e.g.

```
terraform import azurerm_virtual_network_gateway.testGateway /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myGroup1/providers/Microsoft.Network/virtualNetworkGateways/myGateway1
```
