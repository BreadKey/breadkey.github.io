---
title: 안드로이드 체스 앱 프로젝트 2 -Server ER Diagram-
author: breadkey
categories: project
tags: spring
layout: post
---
<div class=mermaid>
erDiagram
User }|--o{ Game : plays
User ||--o{ RankRecord : has
Game }o--o{ RankRecord : captures
Game ||--o{ Move : logs 
</div>