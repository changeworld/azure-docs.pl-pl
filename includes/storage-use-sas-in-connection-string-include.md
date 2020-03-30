---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 2f27c50b1d016265c20102521a137bcbb0646115
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183449"
---
Jeśli masz adres URL podpisu dostępu współdzielonego (SAS), który daje dostęp do zasobów na koncie magazynu, możesz użyć sygnatury dostępu Współdzielonego w ciągu połączenia. Ponieważ sygnatury dostępu Współdzielonego zawiera informacje wymagane do uwierzytelnienia żądania, ciąg połączenia z sygnaturą dostępu Współdzielonego zapewnia protokół, punkt końcowy usługi i poświadczenia niezbędne do uzyskania dostępu do zasobu.

Aby utworzyć ciąg połączenia zawierający sygnaturę dostępu współdzielonego, określ ciąg w następującym formacie:

```
BlobEndpoint=myBlobEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
FileEndpoint=myFileEndpoint;
SharedAccessSignature=sasToken
```

Każdy punkt końcowy usługi jest opcjonalny, chociaż parametry połączenia muszą zawierać co najmniej jeden.

> [!NOTE]
> Korzystanie z protokołu HTTPS z sygnaturą dostępu Współdzielonego jest zalecane jako najlepsze rozwiązanie.
>
> Jeśli określasz sygnaturę dostępu Współdzielonego w ciągu połączenia w pliku konfiguracyjnym, może być konieczne zakodowanie znaków specjalnych w adresie URL.
>
>

### <a name="service-sas-example"></a>Przykład Sygnatury dostępu Współdzielonego
Oto przykład parametry połączenia, który zawiera sygnatury dostępu Współdzielonego usługi dla magazynu obiektów Blob:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
SharedAccessSignature=sv=2015-04-05&sr=b&si=tutorial-policy-635959936145100803&sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

A oto przykład tego samego ciągu połączenia z kodowaniem znaków specjalnych:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
SharedAccessSignature=sv=2015-04-05&amp;sr=b&amp;si=tutorial-policy-635959936145100803&amp;sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

### <a name="account-sas-example"></a>Przykład Sygnatury dostępu Współdzielonego
Oto przykład ciągu połączenia, który zawiera sygnatury dostępu Współdzielonego konta dla blob i magazynu plików. Należy zauważyć, że punkty końcowe dla obu usług są określone:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&spr=https&st=2016-04-12T03%3A24%3A31Z&se=2016-04-13T03%3A29%3A31Z&srt=s&ss=bf&sp=rwl
```

A oto przykład tego samego ciągu połączenia z kodowaniem adresów URL:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&amp;sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&amp;spr=https&amp;st=2016-04-12T03%3A24%3A31Z&amp;se=2016-04-13T03%3A29%3A31Z&amp;srt=s&amp;ss=bf&amp;sp=rwl
```

