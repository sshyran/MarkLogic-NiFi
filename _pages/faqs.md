---
layout: inner
title: Frequently Asked Questions
permalink: /faqs
---

## Apache NiFi FAQs

[Apache NiFi FAQs][apache-faqs]

## MarkLogic NiFi Bundle FAQs

### What version of the MarkLogic NiFi Bundle should I use?

We use a version pattern that mimics the version of NiFi that a MarkLogic NiFi Bundle is built against. If you are using NiFi 1.7.1, you should use the latest 1.7.1.x NiFi bundle. If you are using NiFi 1.8.0, you should use the latest 1.8.0.x NiFi bundle.

As far as the NiFi API compatibility between versions allows, the latest `<NiFi Version>.x` NiFi bundles will have a common code-base and functionality.

### I've encountered an issue. How do I resolve it?

First, be sure to look over the ["Error Resolutions" page][error-resolutions] to find common solutions. If the issue remains unsolved, you can open an issue in the [GitHub issue tracker][github-issue-tracker].

### What version of MarkLogic do I need?

The MarkLogic NiFi bundle requires MarkLogic 9. See the [MarkLogic Java API Guide][java-required-software] for additional details on required software.

### What is supported?

The `ExecuteScriptMarkLogic` is provided as a convenience and is **not** supported due to the nature of evaluating [ad-hoc queries][ad-hoc-queries]. All other processors are supported by MarkLogic.

[ad-hoc-queries]: https://docs.marklogic.com/9.0/guide/java/resourceservices#id_70532
[error-resolutions]: ./error-resolutions
[apache-faqs]: https://cwiki.apache.org/confluence/display/NIFI/FAQs
[github-issue-tracker]: https://github.com/marklogic/nifi/issues
[java-required-software]: https://docs.marklogic.com/9.0/guide/java/intro#id_35911
