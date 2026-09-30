---
title: ''
layout: hextra-home
---

<div class="hx:mb-6 hx:flex hx:flex-col hx:gap-4 hx:justify-center hx:items-center hx:w-full hx:mx-auto">
{{< hextra/hero-badge link="https://github.com/chapar-rest/chapar/releases/tag/v0.9.0" >}}
  <div class="hx:w-2 hx:h-2 hx:rounded-full hx:bg-primary-400"></div>
  <span>v0.9.0 is out, see what's new</span>
  {{< icon name="arrow-circle-right" attributes="height=14" >}}
{{< /hextra/hero-badge >}}

{{< hextra/hero-headline >}}
  The <span class="home-highlight">API Client</span> that simply works
{{< /hextra/hero-headline >}}
</div>

<div class="hx:mb-6 hx:flex hx:justify-center hx:items-center hx:w-full">
{{< hextra/hero-subtitle >}}
  A fast, private and offline API client for your REST, gRPC and GraphQL services
{{< /hextra/hero-subtitle >}}
</div>

<div class="hx:mb-6 hx:flex hx:justify-center hx:items-center hx:w-full hx:gap-3">
{{< hextra/hero-button text="Download" link="/docs/gettingstarted/installation" >}}
{{< hextra/hero-button text="Documentation" link="/docs" style="background: transparent; color: inherit; border: 1px solid currentColor;" >}}
</div>
<div class="hx:mb-6 hx:flex hx:justify-center hx:items-center hx:w-full">
  <p class="hx:text-center hx:text-sm hx:text-gray-600 hx:dark:text-gray-400">
    Free and open source · macOS, Windows and Linux
  </p>
</div>

<div class="hx:w-full hx:mx-auto" style="max-width: 1000px;">
<img src="/images/main-page.png" alt="Chapar sending a REST request to the Chapar mock server" class="hx:w-full hx:h-auto hx:rounded-lg hx:shadow-lg">
</div>

<div class="hx:mt-12"></div>

<div class="hx:w-full hx:mx-auto" style="max-width: 1000px;">
{{< hextra/feature-grid cols="3" >}}
  {{< hextra/feature-card
    title="REST, gRPC and GraphQL"
    subtitle="Every HTTP method and body type, gRPC with reflection or proto files, TLS and streaming, and GraphQL queries."
    link="/docs/usingchapar/http-requests"
    icon="globe-alt"
  >}}
  {{< hextra/feature-card
    title="Fast and Private"
    subtitle="A native app drawn on the GPU. Local-only data, secrets encrypted with your OS keychain, and zero telemetry."
    link="/docs/usingchapar/environments#secret-values"
    icon="lock-closed"
  >}}
  {{< hextra/feature-card
    title="Python Scripting"
    subtitle="Pre and post-request scripts with tests, for HTTP, gRPC and GraphQL, in a sandboxed executor."
    link="/docs/scripting"
    icon="code"
  >}}
  {{< hextra/feature-card
    title="Request Chaining"
    subtitle="Trigger requests, extract values with JSONPath, and share them through environments."
    link="/docs/usingchapar/request-actions"
    icon="switch-horizontal"
  >}}
  {{< hextra/feature-card
    title="Configuration as Files"
    subtitle="Spaces, requests and environments are YAML files you can review and share in git."
    link="/docs/gettingstarted/import-and-export-data"
    icon="document-text"
  >}}
  {{< hextra/feature-card
    title="Import What You Have"
    subtitle="Postman collections and environments, OpenAPI specs and proto files."
    link="/docs/gettingstarted/import-and-export-data#import"
    icon="download"
  >}}
{{< /hextra/feature-grid >}}
</div>

<div class="hx:mt-12"></div>
