---
layout: post
title: Rails RESTful (1) 什麼是REST?
author: gavinkwoe
date: !binary |-
  MjAwNi0xMS0yNyAyMzo1OToxNiArMDgwMA==
date_gmt: !binary |-
  MjAwNi0xMS0yNyAxNTo1OToxNiArMDgwMA==
---
Ruby on Rails 1.2 的一個重要進展是 RESTful，在了解怎麼用之前，我們要先了解什麼是 REST(Representational State Transfer)?
什麼是REST?
REST 是一種分散式超媒體系統(如WWW)的軟體架構風格，你可以想像它是一個良好設計的Web應用程式規則: 一組網路Web頁面(虛擬的狀態機器)，其中 Client 透過點選超連結(狀態變換)，結果是下個Web頁面(表示應用程式的下一個狀態)。
REST 所描述的網路系統包括三個部份:
data elements (resource, resource identifier, representation)
connectors (client, server, cache, resolver, tunnel)
components (origin server, gateway, proxy, user agent)
幾個重點:
Data elements 由標準化介面存取
Components 透過介面傳輸 資源表徵 (representations of resources) 來溝通，而不是直接操作資源本身。
Connectors 提供 component 的抽象介面來溝通，隱藏溝通機制的實作細節。
Components 使用 connectors 來存取，connectors 提供資源的存取或居中。
所有來自 connector 的 requests 必須包含所有必要的資訊來了解該 request，不需要依靠之前的request (即 stateless)
嚴格來說REST符合以下幾個條件:
應用程式的狀態跟功能拆成 resources
每個 resource 使用獨一無二用來當作超連結的通用定位語法(在WWW中即URI)
所有 resources 共用一致的介面在 client 跟 resource 之間轉換狀態，包括:
一組有限的良好定義操作 well-defined operations
一組有限的內容格式 content types,也許包括 可執行的程式碼 code-on-demand (在WWW中即Javascript)
這種通訊協定 protocol (在WWW中即用HTTP) 包含以下特色: 
使用者端/伺服器端 Client/Server
狀態無關 Stateless
可以快取 Cacheable
分層的 Layered
符合 REST principles 的系統稱做 RESTful。 
一般來說REST這個詞彙常被用來描述任何使用XML (or YAML, JSON, plain text) 的簡單介面，而不須靠其他機制(如SOAP)。另外 REST 雖然是個來自 WWW 的架構概念(比WWW晚)，但是這兩者並不是綁在一起，有可能設計一個大型軟體系統符合REST但是不用HTTP協定也不需跟WWW互動，也有可能設計一個簡單的 XML+HTTP 界面使用 RPC model 而不符合 REST principles。
Resources
REST的一個最重要的觀念就是 resources (特定資訊的資源)，每一個 resource 由一個 global identifier (即URI)所表示。為了操作這些  resources，網路的 components (即 clients 跟servers) 透過標準化的介面 (即HTTP) 來溝通並交換這些 resources 的 representations (即實際上傳達資訊的文件).
任意數量的 connectors (如clients, servers, caches, tunnels等) 可以居中 request，但是都不可以 “seeing past” (不需要其他 layer層)。這樣的應用程式跟一個 resource 互動根據兩件事情: resource的URI 跟 要做的動作  &ndash; 它不需要知道是否有  caches, proxies, gateways, firewalls, tunnels, 或其他任何藏在sever之間的東西。這個應用程式只需要知道資訊的格式 (representation)，通常是 HTML或 XML 或圖片什麼的。
REST 有什麼優點?
支援快取 caching 將改善反應時間跟server的負載能力。
因為不必維持連結狀態，大大改善 server 的 scalability 能力。這表示不同server可以處理同一串 requests。
一個瀏覽器就可以存取任一應用程式跟資源，client 端不需使用別的軟體。
在HTTP之上不依存其他機制跟軟體。
跟其他連結方式相比(如RPC)，可以提供相等的功能。
不需要其他的 discovery 機制，因為使用超連結了。
提供比RPC更好的長期相容性，因為 :
如同HTML這種文件具有後前及向後的相容能力
支援新的內容格式不需要丟掉舊的
另外比起RPC-style，URL command line 有極佳的 human-friendly 優點，容易 discovered/ transmitted /scripted/bookmarked 一個 URL。請參考 The Beauty of REST 跟 Tangled in the Threads 的優點介紹。
跟 RPC 的比較
跟 REST Web 應用程式對比的就是 RPC (Remote procedure call，實作上有XML-RPC或SOAP等) 了。RPC 應用程式曝露一或多個網路物件，每個有特定的 functions 可以呼叫。在 client 與物件溝通之前，它必須知道這個物件的知識來操作。
REST 的設計限制了 resource 的面向，它定義了介面  (動詞 verbs 跟內容型別 content types)，導致比RPC更少的型別，但是更多的resource identifiers (名詞nouns)。REST 的設計尋求定義一組 resources 讓 Clients 可以一致性的互動，而且提供超連結在資源之間可以瀏覽，而不需要了解整個 resources 的知識 。Server 提供的表單 forms 也可以被用在 RESTful 的環境來描述 clients 如何建構 URL 來跟特定的resource做溝通。

這張圖指出了REST的三位一體 : Nouns, 有限的 Verbs 跟 Content Types。
需注意的是，根據不同的用法要求跟效率，在不同的應用程式環境中，該使用哪種架構仍有很大討論的空間跟爭議(SOAP的支持者認為REST仍有其侷限，只適合簡單的應用)。REST的重要性在於它有個目前最成功的大型軟體架構應用: Wide World Web。RESTful 是 Web 的天性，也是讓 Web 成功的原因，因此 RESTful 的支持者認為使用 RESTful 架構就是 Web上最好的方法來做到有擴充性(scalable),彈性跟有威力的應用程式。在有這麼多複雜的RPC技術之後，REST因其簡單又有威力的架構近來越來越受到矚目跟重視。
舉例
一個 RPC 應用程式可能會定義以下的操作:
getUser()
addUser()
removeUser()
updateUser()
getLocation()
addLocation()
removeLocation()
updateLocation()
listUsers()
listLocations()
findLocation()
findUser()
Client 的程式碼可能會這樣存取:
exampleAppObject = new ExampleApp(”example.com:1234&Prime;)
exampleAppObject.getUser()
在 REST中，重點在 resources(或稱作 nouns)的多樣性，比如說可能有以下的用法:
http://example.com/users/
http://example.com/users/{user} (one for each user)
http://example.com/findUserForm
http://example.com/locations/
http://example.com/locations/{location} (one for each location)
http://example.com/findLocationForm
Client 的程式碼可能這樣存取:
userResource = new Resource(http://example.com/users/001)
userResource.get()
每個 resource 擁有自己的識別名詞，而 Clients 從單一 resource 開始瀏覽，透過標準操作走訪 resource ，如 GET 下載，PUT更新，DELETE刪除，POST新增，注意到每個物件有自己的URL，而且可以容易被快取，複製跟書籤化(bookmarked)。
參考資源
Representational State Transfer
RestWiki
How to Create a REST Protocol
From :http://ihower.idv.tw/blog/archives/1542 

