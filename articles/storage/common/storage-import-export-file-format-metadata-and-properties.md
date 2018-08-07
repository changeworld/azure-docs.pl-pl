---
title: Usługa Azure Import/Export metadanych format pliku właściwości i | Dokumentacja firmy Microsoft
description: Dowiedz się, jak określić właściwości dla jednego lub więcej obiektów blob, które są dostępne w ramach importowania lub eksportowania zadania i metadanych.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 5a886244b43ad006a95e9be0350d9c69fd987ad9
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526236"
---
# <a name="azure-importexport-service-metadata-and-properties-file-format"></a>Usługa Azure Import/Export właściwości i metadanych format pliku usługi
Można określić właściwości dla co najmniej jednego obiektu blob i metadanych jako część zadania importu lub eksportu. Aby ustawić metadanych lub właściwości dla obiektów blob jest tworzona jako część zadania importu, możesz podać właściwości lub metadanych pliku na dysku twardym, zawierający dane do zaimportowania. Przez zadanie eksportu metadanych i właściwości są zapisywane do pliku metadanych lub właściwości, który znajduje się na dysku twardym, zwrócona do Ciebie.  
  
## <a name="metadata-file-format"></a>Format pliku metadanych  
Format pliku metadanych jest następująca:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
[<metadata-name-1>metadata-value-1</metadata-name-1>]  
[<metadata-name-2>metadata-value-2</metadata-name-2>]  
. . .  
</Metadata>  
```
  
|— Element XML|Typ|Opis|  
|-----------------|----------|-----------------|  
|`Metadata`|Element główny|Element główny plik metadanych.|  
|`metadata-name`|Ciąg|Opcjonalny. XML element Określa nazwę metadanych dla obiektu blob, a jego wartość określa wartość ustawienia metadanych.|  
  
## <a name="properties-file-format"></a>Format pliku właściwości  
Format pliku właściwości jest następująca:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
[<Last-Modified>date-time-value</Last-Modified>]  
[<Etag>etag</Etag>]  
[<Content-Length>size-in-bytes<Content-Length>]  
[<Content-Type>content-type</Content-Type>]  
[<Content-MD5>content-md5</Content-MD5>]  
[<Content-Encoding>content-encoding</Content-Encoding>]  
[<Content-Language>content-language</Content-Language>]  
[<Cache-Control>cache-control</Cache-Control>]  
</Properties>  
```
  
|— Element XML|Typ|Opis|  
|-----------------|----------|-----------------|  
|`Properties`|Element główny|Element główny pliku właściwości.|  
|`Last-Modified`|Ciąg|Opcjonalny. Czas ostatniej modyfikacji dla obiektu blob. Aby uzyskać tylko zadania eksportu.|  
|`Etag`|Ciąg|Opcjonalny. Wartość elementu ETag obiektu blob. Aby uzyskać tylko zadania eksportu.|  
|`Content-Length`|Ciąg|Opcjonalny. Rozmiar obiektu blob w bajtach. Aby uzyskać tylko zadania eksportu.|  
|`Content-Type`|Ciąg|Opcjonalny. Typ zawartości obiektu blob.|  
|`Content-MD5`|Ciąg|Opcjonalny. Skrót MD5 obiektu blob.|  
|`Content-Encoding`|Ciąg|Opcjonalny. Zawartość obiektu blob kodowania.|  
|`Content-Language`|Ciąg|Opcjonalny. Język zawartości obiektu blob.|  
|`Cache-Control`|Ciąg|Opcjonalny. Ciąg kontroli pamięci podręcznej dla obiektu blob.|  

## <a name="next-steps"></a>Kolejne kroki

Zobacz [zestawu właściwości obiektu blob](/rest/api/storageservices/set-blob-properties), [ustawić metadane obiektu Blob](/rest/api/storageservices/set-blob-metadata), i [ustawienie podczas pobierania właściwości i metadanych dla obiektu blob zasobów](/rest/api/storageservices/setting-and-retrieving-properties-and-metadata-for-blob-resources) szczegółowych zasad o ustawienie metadane obiektu blob i właściwości.
