# Check-IP
Check the public ip address of the crawler or other Elastic Cloud based [connectors](https://www.elastic.co/guide/en/enterprise-search/current/build-connector.html). 

## Add ingest pipeline
In Kibana's [console](https://ipinfo.info/html/my_ip_address.php):

```
PUT /_ingest/pipeline/ipinfo
{
  "description": "Add a ip_address field to document when crawling https://ipinfo.info/html/my_ip_address.php",
  "processors": [
    {
      "grok": {
        "field": "body_content",
        "patterns": [
          "My IP %{IP:ip_address} about more detail"
          ],
          "ignore_missing": true,
          "ignore_failure": true
      }
    }
    ]
}
```
## [Configure Email](https://www.elastic.co/guide/en/elasticsearch/reference/current/actions-email.html#configuring-email)  or other [actions](https://www.elastic.co/guide/en/elasticsearch/reference/current/actions.html) to take when IP changes.

## Setup web crawler

## Validate crawler 

In Kibana's console:

```
GET search-ipinfo2/_search
{
  "query": {
    "bool": {
      "filter": [
        {
          "range": {
            "last_crawled_at": {
              "gte": "now-1d/d",
              "lte": "now"
            }
          }
        }
      ],
      "must": {
        "match_phrase": {
          "title.enum": "My IP Address"
        }
      }
    }
  },
  "aggs": {
    "devices": {
      "terms": {
        "field": "title.enum",
        "size": 10000
      },
      "aggs": {
        "distinct_ips": {
          "cardinality": {
            "field": "ip_address.enum"
          }
        },
        "ips": {
          "terms": {
            "field": "ip",
            "size": 10000
          }
        }
      }
    }
  }
}
```


## Add Watch




