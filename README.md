# How to get the IUCN Red List category and other data for a list of species

![ExampleImage](https://imgur.com/ru9Xwx4)

We often have a list of species - a checklist, or a set of species of interest for a project - for which we want to know the IUCN Red List status. Manually searching the IUCN Red List website species by species would be slow, and practically impossible for a list longer than 1,000 species.

This guide shows you how to use the free software OpenRefine to query the IUCN RedList API, using pre-written code that should work for any clean list of species names.

### Open Refine

OpenRefine (formerly Google Refine) is a powerful tool for working with messy data: cleaning it; transforming it from one format into another; and extending it with web services and external data.

OpenRefine is available in English, Chinese, Spanish, French, Russian, Portuguese (Brazil), German, Japanese, Italian, Hungarian, Hebrew, Filipino, Cebuano, Tagalog.

Guides for using OpenRefine are available here: [http://openrefine.org/documentation.html](http://openrefine.org/documentation.html)

Here give step by step instructions for the process needed to query the IUCN Red List. 

Download from: [http://openrefine.org/](http://openrefine.org/)

### IUCN Red List API

The API for the IUCN Red List is like a backdoor to the IUCN data, used by internet-connect computers to make a large number of requests to the Red List. 

You'll need a API token (like a personal password) to use the API. Request a token from here: [https://apiv3.iucnredlist.org/api/v3/token](https://apiv3.iucnredlist.org/api/v3/token)

More documentation on the API is available [https://apiv3.iucnredlist.org/api/v3/docs](https://apiv3.iucnredlist.org/api/v3/docs).

### Species list

You'll need a list of your species of interest. This can be in Excel, CSV, or other standard formats.


## Step by step instructions

The video tutorial starts from Step 3.

1. Request an API token from here: [https://apiv3.iucnredlist.org/api/v3/token](https://apiv3.iucnredlist.org/api/v3/token)
2. Install OpenRefine from here: [http://openrefine.org/](http://openrefine.org/)
3. Format your species list so that only the binomial species name (no authorship or sub-species) are in a column called scientificName
4. Open OpenRefine, and start a new Project by importing your species list
5. Open the Undo/Redo tab
6. Click the Apply button
7. Copy and paste the code below into the window that appears. **Do not click OK until you have completed Step 8**.
8. Replace YOUR_TOKEN_HERE in line 12 with the token provided in Step 1, and click OK.
```markdown
[
  {
    "op": "core/column-addition-by-fetching-urls",
    "description": "Create column RedListAPI at index 1 by fetching URLs based on column scientificName using expression grel:\"http://apiv3.iucnredlist.org/api/v3/species/\"+escape(value, 'html')+\"?token=94c54470dc7cf2360c58f311683616dfabd4fa35526bcae8670e65b11a4e7357\"",
    "engineConfig": {
      "facets": [],
      "mode": "row-based"
    },
    "newColumnName": "RedListAPI",
    "columnInsertIndex": 1,
    "baseColumnName": "scientificName",
    "urlExpression": "grel:\"http://apiv3.iucnredlist.org/api/v3/species/\"+escape(value, 'html')+\"?token=YOUR_TOKEN_HERE\"",
    "onError": "set-to-blank",
    "delay": 2000,
    "cacheResponses": true,
    "httpHeadersJson": [
      {
        "name": "authorization",
        "value": ""
      },
      {
        "name": "user-agent",
        "value": "OpenRefine 3.1 [b90e413]"
      },
      {
        "name": "accept",
        "value": "*/*"
      }
    ]
  },
  {
    "op": "core/column-addition",
    "description": "Create column RedListResult at index 2 based on column RedListAPI using expression grel:value.parseJson().get(\"result\")",
    "engineConfig": {
      "facets": [],
      "mode": "row-based"
    },
    "newColumnName": "RedListResult",
    "columnInsertIndex": 2,
    "baseColumnName": "RedListAPI",
    "expression": "grel:value.parseJson().get(\"result\")",
    "onError": "set-to-blank"
  },
  {
    "op": "core/column-removal",
    "description": "Remove column RedListAPI",
    "columnName": "RedListAPI"
  },
  {
    "op": "core/text-transform",
    "description": "Text transform on cells in column RedListResult using expression grel:value.replace(\"[\", \"\").replace(\"]\", \"\")",
    "engineConfig": {
      "facets": [],
      "mode": "row-based"
    },
    "columnName": "RedListResult",
    "expression": "grel:value.replace(\"[\", \"\").replace(\"]\", \"\")",
    "onError": "keep-original",
    "repeat": false,
    "repeatCount": 10
  },
  {
    "op": "core/column-addition",
    "description": "Create column TempOutput at index 2 based on column RedListResult using expression grel:value.parseJson().get(\"category\")+\"|\"+ value.parseJson().get(\"assessment_date\")+\"|\"+ value.parseJson().get(\"kingdom\")+\"|\"+ value.parseJson().get(\"phylum\")+\"|\"+ value.parseJson().get(\"class\")+\"|\"+ value.parseJson().get(\"order\")+\"|\"+ value.parseJson().get(\"family\")+\"|\"+ value.parseJson().get(\"genus\")+\"|\"+ value.parseJson().get(\"authority\")+\"|\"+ value.parseJson().get(\"main_common_name\")+\"|\"+ value.parseJson().get(\"population_trend\")",
    "engineConfig": {
      "facets": [],
      "mode": "row-based"
    },
    "newColumnName": "TempOutput",
    "columnInsertIndex": 2,
    "baseColumnName": "RedListResult",
    "expression": "grel:value.parseJson().get(\"category\")+\"|\"+ value.parseJson().get(\"assessment_date\")+\"|\"+ value.parseJson().get(\"kingdom\")+\"|\"+ value.parseJson().get(\"phylum\")+\"|\"+ value.parseJson().get(\"class\")+\"|\"+ value.parseJson().get(\"order\")+\"|\"+ value.parseJson().get(\"family\")+\"|\"+ value.parseJson().get(\"genus\")+\"|\"+ value.parseJson().get(\"authority\")+\"|\"+ value.parseJson().get(\"main_common_name\")+\"|\"+ value.parseJson().get(\"population_trend\")",
    "onError": "set-to-blank"
  },
  {
    "op": "core/column-split",
    "description": "Split column TempOutput by separator",
    "engineConfig": {
      "facets": [],
      "mode": "row-based"
    },
    "columnName": "TempOutput",
    "guessCellType": true,
    "removeOriginalColumn": true,
    "mode": "separator",
    "separator": "|",
    "regex": false,
    "maxColumns": 0
  },
  {
    "op": "core/column-rename",
    "description": "Rename column TempOutput 1 to Category",
    "oldColumnName": "TempOutput 1",
    "newColumnName": "Category"
  },
  {
    "op": "core/column-rename",
    "description": "Rename column TempOutput 2 to AssessmentDate",
    "oldColumnName": "TempOutput 2",
    "newColumnName": "AssessmentDate"
  },
  {
    "op": "core/column-rename",
    "description": "Rename column TempOutput 3 to Kingdom",
    "oldColumnName": "TempOutput 3",
    "newColumnName": "Kingdom"
  },
  {
    "op": "core/column-rename",
    "description": "Rename column TempOutput 4 to Phylum",
    "oldColumnName": "TempOutput 4",
    "newColumnName": "Phylum"
  },
  {
    "op": "core/column-rename",
    "description": "Rename column TempOutput 5 to Class",
    "oldColumnName": "TempOutput 5",
    "newColumnName": "Class"
  },
  {
    "op": "core/column-rename",
    "description": "Rename column TempOutput 6 to Order",
    "oldColumnName": "TempOutput 6",
    "newColumnName": "Order"
  },
  {
    "op": "core/column-rename",
    "description": "Rename column TempOutput 7 to Family",
    "oldColumnName": "TempOutput 7",
    "newColumnName": "Family"
  },
  {
    "op": "core/column-rename",
    "description": "Rename column TempOutput 8 to Genus",
    "oldColumnName": "TempOutput 8",
    "newColumnName": "Genus"
  },
  {
    "op": "core/column-rename",
    "description": "Rename column TempOutput 9 to SpeciesAuthority",
    "oldColumnName": "TempOutput 9",
    "newColumnName": "SpeciesAuthority"
  },
  {
    "op": "core/column-rename",
    "description": "Rename column TempOutput 10 to MainCommonName",
    "oldColumnName": "TempOutput 10",
    "newColumnName": "MainCommonName"
  },
  {
    "op": "core/column-rename",
    "description": "Rename column TempOutput 11 to PopulationTrend",
    "oldColumnName": "TempOutput 11",
    "newColumnName": "PopulationTrend"
  },
  {
    "op": "core/column-removal",
    "description": "Remove column RedListResult",
    "columnName": "RedListResult"
  }
]
````
8. Let the code run for a while. This may take some time with long lists, or on slow internet connections. The script is querying the IUCN Red List for every species in your list, one by one. There is a default delay of 2 seconds to avoid overloading the API with requests and avoid temporary blocks.
9. Export the results by clicking Export, and choosing your preferred file format.

For species with no IUCN Red List entry, the columns other than your scientificName column will be blank. Your species names must exactly match the IUCN species name. If there are type-os, or if your taxonomy is different, the species will not match. You can use the GBIF Species Matching tool to help clean your species names, available here: [https://www.gbif.org/tools/species-lookup](https://www.gbif.org/tools/species-lookup). A similar tool called NomenMatch, which allows more choice of taxonomic backbone is available from the taiBIF node here: [http://match.taibif.tw/index.html](http://match.taibif.tw/index.html)
