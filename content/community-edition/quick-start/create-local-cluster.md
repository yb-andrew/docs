---
date: 2016-03-09T00:11:02+01:00
title: Create Local Cluster
weight: 6
---

After [installing YugaByte DB](/community-edition/quick-start/), follow the instructions below to create a local cluster.

<ul class="nav nav-tabs">
  <li class="active">
    <a data-toggle="tab" href="#docker">
      <i class="icon-docker"></i>
      <b>Docker</b>
    </a>
  </li>
  <li >
    <a data-toggle="tab" href="#macos">
      <i class="fa fa-apple" aria-hidden="true"></i>
      <b>macOS</b>
    </a>
  </li>
  <li>
    <a data-toggle="tab" href="#linux">
      <i class="fa fa-linux" aria-hidden="true"></i>
      <b>Linux</b>
    </a>
  </li>
</ul>

<div class="tab-content">
  <div id="docker" class="tab-pane fade in active">
    {{% includeMarkdown "community-edition/quick-start/docker/create-local-cluster.md" /%}}
  </div>
  <div id="macos" class="tab-pane fade">
   {{% includeMarkdown "community-edition/quick-start/binary/create-local-cluster.md" /%}}
  </div>
  <div id="linux" class="tab-pane fade">
    {{% includeMarkdown "community-edition/quick-start/binary/create-local-cluster.md" /%}}
  </div> 
</div>