---
title: Używanie usługi Azure CDN dostęp do obiektów blob z zastosowaniem domen niestandardowych przy użyciu protokołu HTTPS
description: Dowiedz się, jak zintegrować usługę Azure CDN z magazynem obiektów Blob, dostęp do obiektów blob z zastosowaniem domen niestandardowych przy użyciu protokołu HTTPS
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 06/26/2018
ms.author: normesta
ms.reviewer: seguler
ms.subservice: blobs
ms.openlocfilehash: da3a6dcb0d125ac4666bc375e843c57cf12fb2fc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65148404"
---
# <a name="use-azure-cdn-to-access-blobs-with-custom-domains-over-https"></a>Używanie usługi Azure CDN dostęp do obiektów blob z zastosowaniem domen niestandardowych przy użyciu protokołu HTTPS

Usługa Azure Content Delivery Network (Azure CDN) obsługuje teraz protokół HTTPS dla niestandardowych nazw domen. Usługa Azure CDN umożliwia dostęp do obiektów blob przy użyciu niestandardowej nazwy domeny za pośrednictwem protokołu HTTPS. Aby to zrobić, włączania usługi Azure CDN w punkcie końcowym usługi sieci web lub obiektu blob, a następnie mapowania niestandardowej nazwy domeny usługi Azure CDN. Po wykonaniu tych czynności Azure upraszcza włączenie protokołu HTTPS dla domeny niestandardowej za pośrednictwem dostępu jednym kliknięciem i pełnego zarządzania certyfikatami. Brak wzrost normalnej ceny usługi Azure CDN.

Usługa Azure CDN pomaga w ochronie prywatności i integralność danych w przypadku danych aplikacji sieci web, gdy są one przesyłane. Za pomocą protokołu SSL do obsługi ruchu za pośrednictwem protokołu HTTPS, wysokiej dostępności treści Azure przechowuje dane szyfrowane, gdy są wysyłane przez internet. Przy użyciu protokołu HTTPS z usługą Azure CDN pomaga chronić aplikacje internetowe przed atakami.

> [!NOTE]  
> Oprócz zapewniania obsługi protokołu SSL dla niestandardowych nazw domen, usługa Azure CDN może ułatwić skalowanie aplikacji do dostarczania zawartości wymagającej wysokiej przepustowości na świecie. Aby dowiedzieć się więcej, zobacz [Omówienie usługi Azure CDN](../../cdn/cdn-overview.md).

## <a name="quickstart"></a>Szybki start

Aby włączyć protokół HTTPS dla niestandardowego punktu końcowego magazynu obiektów Blob, wykonaj następujące czynności:

1.  [Integrowanie konta usługi Azure storage z usługą Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md).  
    W tym artykule opisano proces tworzenia konta magazynu w witrynie Azure portal, jeśli jeszcze tego nie zrobiłeś.

    > [!NOTE]  
    > Aby dodać punkt końcowy usługi storage w sieci web w wersji zapoznawczej obsługi statycznych witryn internetowych w usłudze Azure Storage, wybierz **Źródło niestandardowe** w **typ źródła** listy rozwijanej. W witrynie Azure portal należy to zrobić z poziomu swojego profilu Azure CDN, zamiast bezpośrednio na koncie magazynu.

2.  [Zamapować zawartość usługi Azure CDN na domenę niestandardową](../../cdn/cdn-map-content-to-custom-domain.md).

3.  [Włączanie protokołu HTTPS w domenie niestandardowej usługi Azure CDN](../../cdn/cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>Sygnatury dostępu współdzielonego

Domyślnie punkty końcowe usługi Blob storage nie zezwalaj na anonimowy dostęp do odczytu. Jeśli punkt końcowy usługi Blob storage jest skonfigurowany do odrzucenia anonimowy dostęp do odczytu, podaj [sygnatury dostępu współdzielonego](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) tokenu do wszystkich żądań do domeny niestandardowej. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem anonimowym w trybie odczytu do kontenerów i obiektów blob](storage-manage-access-to-resources.md).

Usługa Azure CDN nie jest zgodny z ograniczeń, które są dodawane do token sygnatury dostępu współdzielonego. Na przykład wygasają wszystkich tokenów sygnatur dostępu współdzielonego. Zawartość przy użyciu sygnatury dostępu współdzielonego wygasłe możesz uzyskiwać dostęp do czasu tej zawartości jest usuwane z węzłów krawędzi w usłudze Azure CDN. Możesz kontrolować, jak długo dane są buforowane w usłudze Azure CDN, ustawiając nagłówek odpowiedzi pamięci podręcznej. Aby dowiedzieć się więcej, zobacz temat [Zarządzanie wygasaniem obiektów blob usługi Azure Storage w usłudze Azure CDN](../../cdn/cdn-manage-expiration-of-blob-content.md).

Jeśli tworzysz dostępu współdzielonego dwóch lub więcej adresów URL sygnatury dla tego samego punktu końcowego obiektu blob, zaleca się włączenie buforowanie ciągu zapytania dla usługi Azure CDN. Ta akcja zagwarantuje, że Azure traktuje każdy adres URL jako unikatowe jednostki. Aby uzyskać więcej informacji, zobacz [Control Azure CDN caching behavior with query strings](../../cdn/cdn-query-string.md) (Sterowanie zachowaniem buforowania usługi CDN za pomocą ciągów zapytań).

## <a name="http-to-https-redirection"></a>Protokół HTTP do przekierowania protokołu HTTPS

Możesz przekierować ruch HTTP, HTTPS. Ten sposób wymaga użycia usługi Azure CDN — wersja premium, oferowane przez firmę Verizon. [Zastępowanie zachowania HTTP za pomocą usługi Azure CDN aparat reguł](../../cdn/cdn-rules-engine.md) , stosując następującą regułę:

![Protokół HTTP do reguły przekierowania protokołu HTTPS](./media/storage-https-custom-domain-cdn/redirect-to-https.png)

*Nazwa w przypadku punktu końcowego usługi CDN*, która wybierz na liście rozwijanej odnosi się do nazwy, który został skonfigurowany dla punktu końcowego usługi sieć CDN systemu Azure. *Ścieżka do źródła* odnosi się do ścieżki na koncie magazynu źródła przechowywania zawartości statycznej. Jeśli przechowujesz całej zawartości statycznej w jednym kontenerze, należy zastąpić *ścieżka początkowa* nazwą tego kontenera.

Aby uzyskać bardziej zgłębić temat do zasad, zobacz [funkcje aparatu reguł wysokiej dostępności treści Azure](../../cdn/cdn-rules-engine-reference-features.md).

## <a name="pricing-and-billing"></a>Cennik i rozliczenia

Gdy uzyskujesz dostęp do obiektów blob za pomocą usługi Azure CDN, opłaty są naliczane [ceny usługi storage Blob](https://azure.microsoft.com/pricing/details/storage/blobs/) ruchu między węzły brzegowe i pochodzenia (magazyn obiektów Blob). Opłaty są naliczane [cen usługi Azure CDN](https://azure.microsoft.com/pricing/details/cdn/) dla danych, który jest dostępny z węzłów krawędzi.

Załóżmy na przykład, że masz na koncie magazynu w regionie zachodnie stany USA, który uzyskiwany jest dostęp za pośrednictwem usługi Azure CDN. Gdy ktoś jest w Wielkiej Brytanii próbuje uzyskać dostęp obiektu blob w ramach tego konta magazynu za pomocą usługi Azure CDN, Azure najpierw sprawdza, czy dla obiektu blob w węźle krawędzi, znajdujący się najbliżej Wielkiej Brytanii. Jeśli Azure znajdzie obiektu blob, uzyskuje dostęp do kopii i używa ceny usługi Azure CDN, ponieważ usługa Azure CDN jest uzyskanie do niej dostępu. Jeśli platforma Azure nie znaleźć obiektu blob, kopiuje obiekt blob z węzłem krawędzi. Ta akcja powoduje naliczenie opłat wychodzących i transakcji, jak to określono w cen magazynu obiektów Blob. Azure następnie uzyskuje dostęp do plików w węźle krawędzi, co skutkuje rozliczeń usługi Azure CDN.

Na [sieć CDN systemu Azure, cennik](https://azure.microsoft.com/pricing/details/cdn/), obsługa protokołu HTTPS dla nazw domen niestandardowych jest dostępne dla usługi Azure CDN tylko z produktów Verizon — standardowa i Premium.

## <a name="next-steps"></a>Kolejne kroki

* [Konfigurowanie niestandardowej nazwy domeny dla punktu końcowego usługi Blob storage](storage-custom-domain-name.md)
* [Hostowanie statycznej witryny internetowej w usłudze Azure Storage](storage-blob-static-website.md)
