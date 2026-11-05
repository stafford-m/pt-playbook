# 43 - WHOIS

Forward whois lookup
```bash
whois $url -h $hostip
```

Reverse lookup
```bash
whois $ip -h $hostip
```

```text
- Name Server: These servers are part of the Domain Name System (DNS) and help translate domain names into IP addresses, directing traffic to the appropriate web server.
- Registrar: The company through which the domain name was registered. Examples include GoDaddy, Namecheap, and Gandi.
- Registrant Contact: The individual or organization that legally owns the domain. This may include a name, organization, email, and phone number.
- Administrative Contact: The person responsible for managing domain ownership and access. Often the main point of contact for domain changes.
- Technical Contact: The individual or team that manages the domain’s technical setup, such as DNS records or server integration.
- Creation and Expiration Dates: When the domain was first registered and when it is set to expire. This helps assess how long a domain has been active.
- Domain Status: Flags that indicate whether the domain is locked, active, or in transfer
```