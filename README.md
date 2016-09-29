Configuring Apache Nutch 1.12 & Solr 6

<h2>Intro</h2>
This "project" is about setting up Apache Nutch (v1.12) and Solr(6). 
The main change between this configuration and older ones is that Solr 6 does no longr use the schema.xml file for documents parsing.
Instead, Solr uses the notion of managed schema, which config file starts with :
<!-- Solr managed schema - automatically generated - DO NOT EDIT -->

So don't be afraid of this. Backup the genuine file and start editing.
This file is usually located under {SOLR_INSTALL_DIR}/server/solr/{core.dir}/conf/
In my case for instance, it is : /opt/solr621/server/solr/nutchdir/conf/.

You can use the file attached in this project or edit it yourself by adding fields from the schema.xml under {NUTCH_INSTALL}/conf.

<h2>Using NUTCH</h2>
Let /opt/nutch112 be our install directory for Nutch.
We'll be carwling on Amazon.com for somr BSR.
To crawl using Apache Nutch, follow these steps:

```mkdir /opt/nutch112/urls /opt/nutch112/amzcom```

```echo https://www.amazon.com/Best-Sellers-Sports-Outdoors/zgbs/sporting-goods/ref=zg_bs_nav_0 > /opt/nutch112/urls/seeds.txt```

```bin/nutch inject amzcom/db urls```

```bin/nutch generate amzcom/db amzcom/segs```

```s1=`ls -d crawl/segments/2* | tail -1` ```

```bin/nutch fetch $s1```

```bin/nutch parse $s1```

```bin/nutch updatedb amzcom/db``` 

```bin/nutch generate amzcom/db amzcom/segs```

```s1=`ls -d crawl/segments/2* | tail -1` ```

```bin/nutch fetch $s1```

You can repeat these steps as much as you want.

Once done with fetching,

```bin/nutch invertlinks amzcom/linkdb -dir amzcom/segs```

Now, you can either dump the binary segments to see the content or read the link database to show parsed links or move data to Solr.
To dump physical files from stored segments :

```bin/nutch dump -segment amzcom/segs/ -outputDir amzcom/dump0/```

To read the fteched links from the latest segment:

```bin/nutch readlink $s1 -dump amzcom/dumplnk```

To migrate data to Solr (must be running) and you must have at least one core configured with the updated managed-schema.

```bin/nutch solrindex http://localhost:8983/solr/nutch amzcom/db/ -linkdb amzcom/linkdb/ amzcom/segs/20160924132600 -filter -normalize```



