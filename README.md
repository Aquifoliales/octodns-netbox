## octoDNS meets NetBox

A [NetBox](https://github.com/digitalocean/netbox) source for [octoDNS](https://github.com/github/octodns/).

Based on IP address information managed by NetBox,
automatically creating A/AAAA records and their corresponding PTR records.

NetBox does not support multiple FQDNs within 'dns_name'. To give you the choice,
which field you like to use, we gave you the parameter 'fieldselector'.
'dns_name' is the default for NetBox, but previously the field was not provided
and is limited. So someone might use 'description'.


### Example config

The following config will combine the records in `./config/example.com.yaml`
and the dynamically looked up addresses at NetBox.

You must configure `url` and `token` to work with the [NetBox API](https://netbox.readthedocs.io/en/latest/api/overview/).

```yaml
providers:

  config:
    class: octodns.provider.yaml.YamlProvider
    directory: ./config

  netbox:
    class: octodns_netbox.NetboxSource
    url: https://ipam.example.com/api
    token: env/NETBOX_TOKEN
    fieldselector: description

  route53:
    class: octodns.provider.route53.Route53Provider
    access_key_id: env/AWS_ACCESS_KEY_ID
    secret_access_key: env/AWS_SECRET_ACCESS_KEY

zones:

  example.com.:
    sources:
      - config
      - netbox  # will add A/AAAA records
    targets:
      - route53

  192/26.216.202.103.in-addr.arpa.:
    sources:
      - netbox  # will add PTR records (corresponding to A records)
    targets:
      - route53

  2.0.0.c.0.8.d.b.3.0.4.2.ip6.arpa.
    sources:
      - netbox  # will add PTR records (corresponding to AAAA records)
    targets:
      - route53
```
