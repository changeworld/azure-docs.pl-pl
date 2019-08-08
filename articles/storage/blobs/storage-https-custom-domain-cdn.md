---
title: Użyj Azure CDN, aby uzyskać dostęp do obiektów BLOB za pomocą domen niestandardowych za pośrednictwem protokołu HTTPS
description: Dowiedz się, jak zintegrować Azure CDN z usługą BLOB Storage, aby uzyskiwać dostęp do obiektów BLOB za pomocą domen niestandardowych przy użyciu protokołu
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 3ad599182191e41ea43d38260692a7ab46e1af6f
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68845001"
---
# <a name="use-azure-cdn-to-access-blobs-with-custom-domains-over-https"></a>Użyj Azure CDN, aby uzyskać dostęp do obiektów BLOB za pomocą domen niestandardowych za pośrednictwem protokołu HTTPS

Usługa Azure Content Delivery Network (Azure CDN) obsługuje teraz protokół HTTPS dla niestandardowych nazw domen. Za pomocą Azure CDN można uzyskać dostęp do obiektów BLOB za pomocą niestandardowej nazwy domeny za pośrednictwem protokołu HTTPS. W tym celu należy włączyć Azure CDN w obiekcie blob lub w punkcie końcowym sieci Web, a następnie zmapować Azure CDN na niestandardową nazwę domeny. Po zakończeniu platforma Azure upraszcza włączenie protokołu HTTPS dla domeny niestandardowej za pomocą dostępu jednym kliknięciem i pełne zarządzanie certyfikatami. W normalnych cenach Azure CDN nie ma wzrostu.

Azure CDN pomaga chronić prywatność i integralność danych aplikacji sieci Web podczas ich przesyłania. Dzięki użyciu protokołu SSL do obsługi ruchu za pośrednictwem protokołu HTTPS Azure CDN dane są szyfrowane, gdy są wysyłane przez Internet. Korzystanie z protokołu HTTPS z Azure CDN pomaga chronić aplikacje sieci Web przed atakami.

> [!NOTE]  
> Oprócz zapewniania obsługi protokołu SSL dla niestandardowych nazw domen, Azure CDN może pomóc w skalowaniu aplikacji do dostarczania zawartości o wysokiej przepustowości na całym świecie. Aby dowiedzieć się więcej, zobacz [omówienie Azure CDN](../../cdn/cdn-overview.md).

## <a name="quickstart"></a>Szybki start

Aby włączyć protokół HTTPS dla niestandardowego punktu końcowego magazynu obiektów blob, wykonaj następujące czynności:

1.  [Zintegruj konto usługi Azure Storage z usługą Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md).  
    W tym artykule przedstawiono proces tworzenia konta magazynu w Azure Portal, jeśli jeszcze tego nie zrobiono.

    > [!NOTE]  
    > Aby dodać punkt końcowy sieci Web usługi Storage w wersji zapoznawczej obsługi statycznych witryn sieci Web w usłudze Azure Storage, wybierz pozycję **niestandardowe źródło** na liście rozwijanej **Typ źródła** . W Azure Portal należy to zrobić z poziomu profilu Azure CDN, a nie bezpośrednio na koncie magazynu.

2.  [Mapuj zawartość Azure CDN na domenę](../../cdn/cdn-map-content-to-custom-domain.md)niestandardową.

3.  [Włącz protokół https w domenie niestandardowej Azure CDN](../../cdn/cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>Sygnatury dostępu współdzielonego

Domyślnie punkty końcowe magazynu obiektów BLOB nie zezwalają na anonimowy dostęp do odczytu. Jeśli punkt końcowy magazynu obiektów BLOB jest skonfigurowany tak, aby nie zezwalać na anonimowy dostęp do odczytu, podaj token [sygnatury dostępu](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) współdzielonego w każdym żądaniu do domeny niestandardowej. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem anonimowym w trybie odczytu do kontenerów i obiektów blob](storage-manage-access-to-resources.md).

Azure CDN nie przestrzega żadnych ograniczeń, które są dodawane do tokenu sygnatury dostępu współdzielonego. Na przykład wszystkie tokeny sygnatury dostępu współdzielonego wygasną. Nadal możesz uzyskiwać dostęp do zawartości przy użyciu sygnatury dostępu współdzielonego, dopóki ta zawartość nie zostanie przeczyszczona z węzłów krawędzi Azure CDN. Możesz kontrolować, jak długo dane są buforowane w usłudze Azure CDN, ustawiając nagłówek odpowiedzi pamięci podręcznej. Aby dowiedzieć się, jak to zrobić, zobacz [Zarządzanie wygasaniem obiektów BLOB usługi Azure Storage w Azure CDN](../../cdn/cdn-manage-expiration-of-blob-content.md).

Jeśli tworzysz dwa lub więcej adresów URL sygnatury dostępu współdzielonego dla tego samego punktu końcowego obiektu BLOB, zalecamy włączenie buforowania ciągu zapytania dla Azure CDN. Ta akcja zapewnia, że platforma Azure traktuje każdy adres URL jako unikatową jednostkę. Aby uzyskać więcej informacji, zobacz [Control Azure CDN caching behavior with query strings](../../cdn/cdn-query-string.md) (Sterowanie zachowaniem buforowania usługi CDN za pomocą ciągów zapytań).

## <a name="http-to-https-redirection"></a>Przekierowywanie protokołu HTTP do protokołu HTTPS

Ruch HTTP można przekierować do protokołu HTTPS. To wymaga użycia oferty Azure CDN Premium z Verizon. [Zastąp zachowanie http aparatem reguł Azure CDN](../../cdn/cdn-rules-engine.md) , stosując następującą regułę:

![Reguła przekierowania HTTP do HTTPS](./media/storage-https-custom-domain-cdn/redirect-to-https.png)

*Sieć CDN — nazwa punktu końcowego*, która została wybrana na liście rozwijanej, odnosi się do nazwy skonfigurowanej dla punktu końcowego Azure CDN. *Ścieżka pierwotna* odnosi się do ścieżki w ramach pierwotnego konta magazynu, w którym jest przechowywana zawartość statyczna. Jeśli przechowujesz całą zawartość statyczną w pojedynczym kontenerze, Zastąp *element Origin-Path* nazwą tego kontenera.

Aby uzyskać bardziej szczegółowy szczegółowe reguł, zobacz [funkcje aparatu reguł Azure CDN](../../cdn/cdn-rules-engine-reference-features.md).

## <a name="pricing-and-billing"></a>Cennik i rozliczenia

Podczas uzyskiwania dostępu do obiektów BLOB za pomocą Azure CDN są naliczane [ceny usługi BLOB Storage](https://azure.microsoft.com/pricing/details/storage/blobs/) dotyczące ruchu między węzłami krawędzi a źródłem (BLOB Storage). Opłaty za [Azure CDN](https://azure.microsoft.com/pricing/details/cdn/) są naliczane za dane, do których dostęp odbywa się z węzłów brzegowych.

Załóżmy na przykład, że masz konto magazynu w regionie zachodnie stany USA, do którego uzyskujesz dostęp za pośrednictwem Azure CDN. Gdy ktoś w Wielkiej Brytanii próbuje uzyskać dostęp do obiektu BLOB w ramach tego konta magazynu za pośrednictwem Azure CDN, platforma Azure najpierw sprawdzi, czy obiekt BLOB znajduje się w węźle brzegowym, który znajduje się najbliżej Zjednoczonego Królestwa. Jeśli platforma Azure odnajdzie obiekt BLOB, uzyskuje dostęp do kopii i używa cen Azure CDN, ponieważ Azure CDN uzyskuje do niej dostęp. Jeśli platforma Azure nie odnajdzie obiektu BLOB, kopiuje obiekt BLOB do węzła brzegowego. Ta akcja powoduje naliczanie opłat za wyjście i transakcję, jak określono w cenniku magazynu obiektów BLOB. Następnie platforma Azure uzyskuje dostęp do pliku w węźle brzegowym, co skutkuje rozliczeniami Azure CDN.

Na [stronie cennika Azure CDN](https://azure.microsoft.com/pricing/details/cdn/)obsługa protokołu HTTPS dla nazw domen niestandardowych jest dostępna dla Azure CDN tylko w przypadku produktów Verizon Standard i Premium.

## <a name="next-steps"></a>Kolejne kroki

* [Konfigurowanie niestandardowej nazwy domeny dla punktu końcowego usługi BLOB Storage](storage-custom-domain-name.md)
* [Hostowanie statycznej witryny internetowej w usłudze Azure Storage](storage-blob-static-website.md)
