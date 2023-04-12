---
title: "Hooks"
date: 2023-04-12T12:31:38+02:00
draft: true
categories: [asd,asd2]
tags: [test whatever, asdasd]
---

# Abstract

Hooks könnten eine Möglichkeit sein, Informationen wieder in Node-Logik zurück zu führen. Dies wird zum Beispiel bereits als ActivityActions in Pipelines genutzt: Manche Activities können andere Activities für bestimmte Zwecke aufrufen.

## Example

```yaml
id: mypipeline
type: Pipeline
steps:
  - cache.getOrCreate
    onCreate:
      fetch: http://www....
    expiration: 00:10:00
    provider: filesystem
  - script: |
    var document = scrape.parse($input);
    document.querySelectorAll("...");
```
*Pipeline Beispiel*

Hier in dem Beispiel wird eine cache.getOrCreate Activity verwendet. Diese schaut in einem vordefinierten Cache, ob hier bereits ein Eintrag vorhanden ist. Wenn nicht, wird einer mithilfe der angegebenen Activity erstellt. Wie immer kann dies auch eine weitere Pipeline sein, in diesem Beispiel wird ein HTTP Request (fetch) verwendet.

Diese Art von "Hooks" fördert die funktionale Herangehensweise. Durch diese Konstellation können alle Activities im System gecached werden, egal ob fetch, sql-Abfrage oder benutzergenerierter Inhalt.

In den Workflows möchten wir die Hooks ebenfalls integrieren. Hier sind die Hooks aber nicht nur einfache Funktionen die ausgeführt werden wie bei einer Pipeline, sondern je nachdem komplexe Vorgänge. Dafür ist folgendes angedacht: Ein Hook in einem Flow kann sowohl eine einfache Funktion/Activity sein, als auch ein "Subflow", welcher via ExecutionPointer verfolgt wird. Dieses Vorgehen wird beispielsweise bei Schleifen verwendet:

## Example 2

```yaml
id: myflow
type: Flow
nodes:
  - type: ForEach
    id: foreach1
    comment: Beispiel mit Subflow
    onIteration:
      type: subflow
      flow:
        nodes:
          - ...
  - type: ForEach
    id: foreach2
    comment: Beispiel mit Activity
    onIteration:
      type: activity
      activity: pipeline
      parameters:
        steps:
          - dtk.basics.log: ...
    
```
