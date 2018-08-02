---
title: Dostęp do obiektów blob z zastosowaniem domen niestandardowych przy użyciu protokołu HTTPS przy użyciu usługi Azure CDN
description: Dowiedz się, jak zintegrować usługi Azure CDN z magazynu obiektów blob, dostęp do obiektów blob z zastosowaniem domen niestandardowych przy użyciu protokołu HTTPS
services: storage
author: michaelhauss
ms.service: storage
ms.topic: article
ms.date: 06/26/2018
ms.author: mihauss
ms.component: blobs
ms.openlocfilehash: 7c4acc7d0832442b94735619ea3a01cb319da993
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2018
ms.locfileid: "39398259"
---
# <a name="using-the-azure-cdn-to-access-blobs-with-custom-domains-over-https"></a>Dostęp do obiektów blob z zastosowaniem domen niestandardowych przy użyciu protokołu HTTPS przy użyciu usługi Azure CDN
Usługa Azure Content Delivery Network (CDN) obsługuje teraz protokół HTTPS dla niestandardowych nazw domen. Możesz korzystać z tej funkcji w celu dostępu do magazynu obiektów blob przy użyciu domeny niestandardowej przy użyciu protokołu HTTPS. Aby to zrobić, musisz najpierw do włączania usługi Azure CDN w punkcie końcowym usługi sieci web lub obiektów blob i mapowania sieci CDN niestandardowej nazwy domeny. Po wykonaniu tych kroków, włączenie protokołu HTTPS dla domeny niestandardowej został uproszczony dzięki możliwości włączenia jednym kliknięciem, pełnego zarządzania certyfikatami i wszystko bez żadnych dodatkowych kosztów normalnym cennikiem sieci CDN.

Ta możliwość jest ważne, ponieważ pozwala na ochronę prywatności i integralności danych przesyłanych danych aplikacji poufnych sieci web. Przy użyciu protokołu SSL do obsługi ruchu za pośrednictwem protokołu HTTPS zapewnia, że dane są szyfrowane, gdy są wysyłane przez internet. Protokół HTTPS zapewnia zaufania i uwierzytelnianie i chroni aplikacje internetowe przed atakami.

> [!NOTE]  
> Oprócz zapewniania obsługi protokołu SSL dla niestandardowych nazw domen, usługa Azure CDN może ułatwić skalowanie aplikacji do dostarczania zawartości wymagającej wysokiej przepustowości na świecie. Aby dowiedzieć się więcej, zapoznaj się z [Omówienie usługi Azure CDN](../../cdn/cdn-overview.md).

## <a name="quick-start"></a>Szybki start
Poniżej przedstawiono kroki wymagane do włączania protokołu HTTPS dla punktu końcowego magazynu obiektów blob niestandardowych:

1.  [Integrowanie konta usługi Azure storage z usługą Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md).
    W tym artykule opisano proces tworzenia konta magazynu w witrynie Azure Portal, jeśli nie zostało to jeszcze zrobione.

    > [!NOTE]  
    > W trakcie okresu zapoznawczego obsługi statycznych witryn internetowych w usłudze Azure Storage wybierz "źródła niestandardowego" z "origin" menu rozwijane typu można dodać punktu końcowego usługi storage w sieci web. W witrynie Azure Portal należy to zrobić z poziomu swojego profilu CDN, a nie bezpośrednio na koncie magazynu.

2.  [Zamapować zawartość usługi Azure CDN na domenę niestandardową](../../cdn/cdn-map-content-to-custom-domain.md).
3.  [Włączanie protokołu HTTPS w domenie niestandardowej usługi Azure CDN](../../cdn/cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>Sygnatury dostępu współdzielonego
Jeśli punkt końcowy usługi blob storage jest skonfigurowany do odrzucenia anonimowy dostęp do odczytu, musisz podać [sygnatury dostępu współdzielonego (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) token w każdym żądaniu możesz wprowadzić domenę niestandardową. Domyślnie punkty końcowe usługi blob storage nie zezwalaj na anonimowy dostęp do odczytu. Zobacz [Zarządzanie anonimowy dostęp do odczytu do kontenerów i obiektów blob](storage-manage-access-to-resources.md) więcej informacji na temat sygnatur dostępu współdzielonego.

Usługa Azure CDN nie przestrzega ograniczeń dodane do tokenu sygnatury dostępu Współdzielonego. Na przykład wszystkie tokeny sygnatur dostępu Współdzielonego ustalić czas wygaśnięcia. Oznacza to, że zawartość jest nadal dostępny przy użyciu sygnatury dostępu Współdzielonego wygasłe aż tej zawartości jest usuwane z węzłów brzegowych usługi CDN. Można kontrolować, jak długo dane są buforowane w usłudze CDN, ustawiając nagłówek odpowiedzi w pamięci podręcznej. Zobacz [Zarządzanie wygaśnięcia obiektów blob usługi Azure Storage w usłudze Azure CDN](../../cdn/cdn-manage-expiration-of-blob-content.md) instrukcje.

Jeśli tworzysz wiele URL sygnatury dostępu Współdzielonego dla tego samego punktu końcowego obiektu blob, zaleca się włączenie buforowania dla usługi Azure CDN ciągu kwerendy. To, aby upewnić się, że każdy adres URL jest traktowane jako unikatowe jednostki. Zobacz [kontrolowanie Azure działanie buforowania usługi CDN z ciągami zapytań](../../cdn/cdn-query-string.md) Aby uzyskać więcej informacji.

## <a name="http-to-https-redirection"></a>Protokół HTTP do przekierowania protokołu HTTPS
Można wybrać przekierować ruch HTTP do HTTPS. Wymaga to użycia usługi Azure CDN — wersja premium, oferowane przez firmę Verizon. Musisz [zachowanie zastąpienia HTTP przy użyciu usługi Azure CDN aparat reguł](../../cdn/cdn-rules-engine.md) z następującą regułą:

![](./media/storage-https-custom-domain-cdn/redirect-to-https.png)

"Sieć Cdn-endpoint-name" odnosi się do nazwy, który został skonfigurowany dla punktu końcowego usługi CDN. Z listy rozwijanej można wybrać tę wartość. "— Ścieżka do źródła" odnosi się do ścieżki na koncie magazynu źródła, w którym znajduje się Twoja zawartość statyczną. Jeśli hostujesz całej zawartości statycznej w jednym kontenerze, Zamień na nazwę kontenera "— Ścieżka do źródła".

Aby uzyskać bardziej zgłębić temat do reguł, zobacz [funkcje aparatu reguł wysokiej dostępności treści Azure](../../cdn/cdn-rules-engine-reference-features.md).

## <a name="pricing-and-billing"></a>Cennik i rozliczenia
Gdy uzyskujesz dostęp do obiektów blob za pomocą usługi Azure CDN, opłaty są naliczane [ceny usługi storage Blob](https://azure.microsoft.com/pricing/details/storage/blobs/) ruchu między węzły brzegowe i pochodzenia (Blob storage) i [CDN ceny](https://azure.microsoft.com/pricing/details/cdn/) danych dostępne z węzłów krawędzi.

Na przykład załóżmy, że masz na koncie magazynu w regionie zachodnie stany USA, który jest uzyskiwany za pomocą usługi Azure CDN. W Zjednoczonym Królestwie próbie dostępu obiektów blob w ramach tego konta magazynu za pośrednictwem sieci CDN, Azure najpierw sprawdza węzła brzegowego najbliżej UK dla tego obiektu blob. Jeśli znaleziono, wówczas uzyskuje dostęp do tej kopii obiektu blob i użyje ceny usługi CDN, ponieważ jest on dostępny w sieci CDN. Jeśli nie zostanie znaleziony, Azure kopiuje obiekt blob do węzła krawędzi, który będzie spowodować naliczenie opłat wychodzących i transakcji, jak to określono w cen magazynu obiektów Blob, a następnie uzyskać dostęp do pliku w węźle krawędzi, co spowoduje, że w sieci CDN, opłaty.

Po wyświetleniu [stronę z cennikiem sieci CDN](https://azure.microsoft.com/pricing/details/cdn/), należy pamiętać, że obsługa protokołu HTTPS dla nazw domen niestandardowych jest dostępne tylko dla usługi Azure CDN z produktów firmy Verizon (Standard i Premium).

## <a name="next-steps"></a>Kolejne kroki
* [Konfigurowanie niestandardowej nazwy domeny dla punktu końcowego usługi Blob storage](storage-custom-domain-name.md)
* [Hostowania statycznej witryny internetowej w usłudze Azure Storage (wersja zapoznawcza)](storage-blob-static-website.md)
