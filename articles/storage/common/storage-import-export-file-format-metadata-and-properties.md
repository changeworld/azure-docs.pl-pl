---
title: Azure format importu/eksportu metadanych i właściwości pliku | Dokumentacja firmy Microsoft
description: Dowiedz się, jak określać metadanych i właściwości dla jednego lub więcej obiektów blob, które są częścią importu lub eksportu zadania.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: 840364c6-d9a8-4b43-a9f3-f7441c625069
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 3f728ad94cdcbd32092b677f11a737ae91376720
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873653"
---
# <a name="azure-importexport-service-metadata-and-properties-file-format"></a>Azure Import/Eksport usługi metadanych i właściwości format pliku
Można określić właściwości dla co najmniej jednego obiektu blob i metadanych jako część zadania importu lub eksportu. Aby ustawić właściwości dla obiektów blob jest tworzony jako część zadania importu lub metadane, musisz podać metadanych lub właściwości pliku na dysku twardym, zawierający dane do zaimportowania. Dla zadania eksportu metadanych i właściwości są zapisywane w pliku metadanych lub właściwości, który znajduje się na dysku twardym zwracane.  
  
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
  
|XML Element|Typ|Opis|  
|-----------------|----------|-----------------|  
|`Metadata`|Element główny|Element główny pliku metadanych.|  
|`metadata-name`|Ciąg|Opcjonalny. XML element Określa nazwę metadane obiektu blob, a jego wartość określa wartość ustawienia metadanych.|  
  
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
  
|XML Element|Typ|Opis|  
|-----------------|----------|-----------------|  
|`Properties`|Element główny|Element główny pliku właściwości.|  
|`Last-Modified`|Ciąg|Opcjonalny. Czas ostatniej modyfikacji obiektu blob. Tylko zadania eksportu.|  
|`Etag`|Ciąg|Opcjonalny. Wartość ETag obiektu blob. Tylko zadania eksportu.|  
|`Content-Length`|Ciąg|Opcjonalny. Rozmiar obiektu blob w bajtach. Tylko zadania eksportu.|  
|`Content-Type`|Ciąg|Opcjonalny. Typ zawartości obiektu blob.|  
|`Content-MD5`|Ciąg|Opcjonalny. Skrót MD5 obiektu blob.|  
|`Content-Encoding`|Ciąg|Opcjonalny. Zawartość obiektu blob kodowania.|  
|`Content-Language`|Ciąg|Opcjonalny. Język zawartości obiektu blob.|  
|`Cache-Control`|Ciąg|Opcjonalny. Ciąg kontroli pamięci podręcznej dla obiektu blob.|  

## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw właściwości obiektu blob](/rest/api/storageservices/set-blob-properties), [ustawić metadane obiektu Blob](/rest/api/storageservices/set-blob-metadata), i [ustawienie podczas pobierania właściwości i metadanych dla obiektu blob zasobów](/rest/api/storageservices/setting-and-retrieving-properties-and-metadata-for-blob-resources) szczegółowe zasady dotyczące ustawiania właściwości i metadane obiektu blob.
