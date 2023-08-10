# Search Engine for Opera Music

This guide outlines the steps to perform web crawling, indexing, and using Apache Nutch and Solr for search and analysis.

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Step 1: Cleaning and Setup](#step-1-cleaning-and-setup)
3. [Step 2: Crawling](#step-2-crawling)
4. [Step 3: Indexing with Solr](#step-3-indexing-with-solr)
5. [Step 4: Webgraph and PageRank](#step-4-webgraph-and-pagerank)
6. [Conclusion](#conclusion)

## Prerequisites

- [Apache Nutch](https://www.apache.org/dyn/closer.lua/nutch/1.19/apache-nutch-1.19-bin.tar.gz) (version: 1.19)
- [Solr](https://www.apache.org/dyn/closer.lua/lucene/solr/8.11.2/solr-8.11.2.tgz?action=download) (version: 8.11.2)
- Terminal or Command Prompt

## Step 1: Cleaning and Setup

1. Delete the existing crawl and dump directories:
    ```
    rm -r crawl/
    rm -r dump/
    ```

2. Open the `apache-nutch-xxxx` directory.

## Step 2: Crawling

1. Create a directory named `urls` and create a `seed.txt` file containing the list of URLs.

2. Inject URLs:
    ```
    bin/nutch inject crawl/crawldb urls
    ```

3. Generate crawldb and segments:
    ```
    bin/nutch generate crawl/crawldb crawl/segments
    ```

4. Fetch, parse, and update:
    ```
    bin/nutch fetch crawl/segments/2XXXXXXXXXXX/
    bin/nutch parse crawl/segments/2XXXXXXXXXXX/
    bin/nutch updatedb crawl/crawldb crawl/segments/2XXXXXXXXXXX/
    ```

5. Start crawling:
    ```
    bin/crawl --num-threads 120 crawl 2
    ```
    You can use --num-threads to increase/decrease the CPU threads

6. Store crawled links in the dump directory:
    ```
    bin/nutch readlinkdb crawl/linkdb/ -dump dump/linkdb
    ```

## Step 3: Indexing with Solr

1. Start Solr server:
    ```
    cd path/to/solr/directory
    bin/solr start -force
    ```

2. Verify Solr server is active: Open http://localhost:8983/ in a browser.

3. Invert links and perform indexing:
    ```
    bin/nutch invertlinks crawl/linkdb -dir crawl/segments
    bin/nutch index crawl/crawldb/ -linkdb crawl/linkdb -dir crawl/segments/ -filter -normalize -deleteGone
    ```

## Step 4: Webgraph and PageRank

1. Create the webgraph:
    ```
    bin/nutch webgraph -segmentDir crawl/segments/ -webgraphdb crawl/webgraphdb
    ```

2. Run PageRank algorithm iteratively:
    ```
    bin/nutch linkrank -webgraphdb crawl/webgraphdb/ > pageRank_scores.txt
    ```

3. Update PageRank scores in the crawled database:
    ```
    bin/nutch scoreupdater -crawldb crawl/crawldb -webgraphdb crawl/webgraphdb/
    ```

## Conclusion

This guide provides a step-by-step process for web crawling, indexing, and using Apache Nutch and Solr for search and analysis. By following these instructions, you can efficiently gather and analyze data from the web.

For more information, refer to the official documentation of [Apache Nutch](https://nutch.apache.org) and [Solr](https://lucene.apache.org/solr/).
