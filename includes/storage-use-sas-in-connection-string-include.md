---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 2f27c50b1d016265c20102521a137bcbb0646115
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183449"
---
Jeśli użytkownik ma adres URL sygnatury (SAS) dostępu współdzielonego, który udziela dostępu do zasobów na koncie magazynu, można użyć sygnatury dostępu Współdzielonego w parametrach połączenia. Ponieważ sygnatury dostępu Współdzielonego zawiera informacje wymagane do uwierzytelnienia żądania, parametry połączenia przy użyciu sygnatury dostępu Współdzielonego zapewnia protokół, punkt końcowy usługi i niezbędne poświadczenia, aby uzyskać dostęp do zasobu.

Aby utworzyć parametry połączenia, który zawiera sygnatury dostępu współdzielonego, należy określić ciąg w następującym formacie:

```
BlobEndpoint=myBlobEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
FileEndpoint=myFileEndpoint;
SharedAccessSignature=sasToken
```

Każdy punkt końcowy usługi jest opcjonalne, chociaż parametry połączenia muszą zawierać co najmniej jeden.

> [!NOTE]
> Przy użyciu protokołu HTTPS przy użyciu sygnatury dostępu Współdzielonego jest zalecane zgodnie z zaleceniami.
>
> Jeśli określisz sygnatury dostępu Współdzielonego w ciągu połączenia w pliku konfiguracji, może być konieczne kodowanie znaków specjalnych w adresie URL.
>
>

### <a name="service-sas-example"></a>Przykład sygnatury dostępu Współdzielonego usługi
Oto przykład parametrów połączenia, który zawiera sygnatury dostępu Współdzielonego usługi Blob storage:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
SharedAccessSignature=sv=2015-04-05&sr=b&si=tutorial-policy-635959936145100803&sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

A Oto przykład te same parametry połączenia z kodowaniem znaków specjalnych:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
SharedAccessSignature=sv=2015-04-05&amp;sr=b&amp;si=tutorial-policy-635959936145100803&amp;sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

### <a name="account-sas-example"></a>Przykład użycia sygnatury dostępu Współdzielonego konta
Oto przykład parametrów połączenia, który zawiera sygnatury dostępu Współdzielonego konta magazynu obiektów Blob i plików. Należy zwrócić uwagę na to, że podano punktów końcowych dla obu usług:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&spr=https&st=2016-04-12T03%3A24%3A31Z&se=2016-04-13T03%3A29%3A31Z&srt=s&ss=bf&sp=rwl
```

A Oto przykład tych samych parametrach połączenia przy użyciu kodowania adresu URL:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&amp;sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&amp;spr=https&amp;st=2016-04-12T03%3A24%3A31Z&amp;se=2016-04-13T03%3A29%3A31Z&amp;srt=s&amp;ss=bf&amp;sp=rwl
```

