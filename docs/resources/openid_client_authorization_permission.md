# keycloak_openid_client_authorization_permission

Allows you to manage openid Client Authorization Permissions.

### Example Usage

```hcl
data "keycloak_realm" "realm" {
	realm = "realm"
}

resource keycloak_openid_client test {
	client_id                = "client_id"
	realm_id                 = data.keycloak_realm.realm.id
	access_type              = "CONFIDENTIAL"
	service_accounts_enabled = true
	authorization {
		policy_enforcement_mode = "ENFORCING"
	}
}

data keycloak_openid_client_authorization_policy default {
	realm_id           = data.keycloak_realm.realm.id
	resource_server_id = "${keycloak_openid_client.test.resource_server_id}"
	name               = "default"
}

resource keycloak_openid_client_authorization_resource test {
	resource_server_id = "${keycloak_openid_client.test.resource_server_id}"
	name               = "resource_name"
	realm_id           = data.keycloak_realm.realm.id

	uris = [
		"/endpoint/*"
	]
}

resource keycloak_openid_client_authorization_scope test {
	resource_server_id = "${keycloak_openid_client.test.resource_server_id}"
	name               = "scope_name"
	realm_id           = data.keycloak_realm.realm.id
}

resource keycloak_openid_client_authorization_permission test {
	resource_server_id = "${keycloak_openid_client.test.resource_server_id}"
	realm_id           = data.keycloak_realm.realm.id
	name               = "permission_name"
	policies           = ["${data.keycloak_openid_client_authorization_policy.default.id}"]
	resources          = ["${keycloak_openid_client_authorization_resource.test.id}"]

}
```

		Schema: map[string]*schema.Schema{
			"resource_server_id": {
				Type:     schema.TypeString,
				Required: true,
				ForceNew: true,
			},
			"realm_id": {
				Type:     schema.TypeString,
				Required: true,
				ForceNew: true,
			},
			"name": {
				Type:     schema.TypeString,
				Required: true,
			},
			"description": {
				Type:     schema.TypeString,
				Optional: true,
			},
			"decision_strategy": {
				Type:         schema.TypeString,
				Optional:     true,
				ValidateFunc: validation.StringInSlice(keycloakOpenidClientResourcePermissionDecisionStrategies, false),
				Default:      "UNANIMOUS",
			},
			"policies": {
				Type:     schema.TypeSet,
				Elem:     &schema.Schema{Type: schema.TypeString},
				Optional: true,
			},
			"resources": {
				Type:     schema.TypeSet,
				Elem:     &schema.Schema{Type: schema.TypeString},
				Optional: true,
			},
			"resource_type": {
				Type:     schema.TypeString,
				Optional: true,
			},
			"scopes": {
				Type:     schema.TypeSet,
				Elem:     &schema.Schema{Type: schema.TypeString},
				Optional: true,
			},
			"type": {
				Type:         schema.TypeString,
				Optional:     true,
				Default:      "resource",
				ValidateFunc: validation.StringInSlice(keycloakOpenidClientPermissionTypes, false),
			},
		},


### Argument Reference

The following arguments are supported:

- `realm_id` - (Required) The realm this group exists in.
- `resource_server_id` - (Required) The ID of the resource server.
- `name` - (Required) The name of the permission.
- `description` - (Optional) A description for the authorization permission.
- `decision_strategy` - (Optional) The decision strategy, can be one of `UNANIMOUS`, `AFFIRMATIVE`, or `CONSENSUS`. Defaults to `UNANIMOUS`.
- `policies` - (Optional) A list of policy IDs.
- `resources` - (Optional) A list of resource IDs.
- `resource_type` - (Optional) A resource type.
- `scopes` - (Optional) A list of scopes.
- `type` - (Optional) The type of permission, can be one of `resource` or `scope`.

### Attributes Reference

In addition to the arguments listed above, the following computed attributes are exported:

- `id` - Permission ID representing the permission.
