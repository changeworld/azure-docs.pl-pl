---
title: Dostęp do obiektów blob z domenami niestandardowymi za pośrednictwem protokołu HTTPS przy użyciu usługi Azure CDN
description: Dowiedz się, jak zintegrować Azure CDN z magazynu obiektów blob można uzyskać dostępu do obiektów blob z domenami niestandardowymi za pośrednictwem protokołu HTTPS
services: storage
documentationcenter: ''
author: michaelhauss
manager: vamshik
editor: tysonn
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: mihauss
ms.openlocfilehash: b3b1b5064e51b68bb64cb8c4dbec6075705795d6
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37025853"
---
# <a name="using-the-azure-cdn-to-access-blobs-with-custom-domains-over-https"></a>Dostęp do obiektów blob z domenami niestandardowymi za pośrednictwem protokołu HTTPS przy użyciu usługi Azure CDN
Azure sieci dostarczania zawartości (CDN) obsługuje teraz HTTPS dla niestandardowych nazw domen. Można wykorzystać tę funkcję, aby można było uzyskać dostępu do magazynu obiektów blob przy użyciu domeny niestandardowej przy użyciu protokołu HTTPS. Aby to zrobić, musisz najpierw włączyć usługi Azure CDN dla punktu końcowego obiektu blob lub sieci web i mapowanie sieci CDN do niestandardowej nazwy domeny. Po wykonaniu tych kroków, włączanie obsługi protokołu HTTPS dla domeny niestandardowej jest uproszczone, za pomocą jednego kliknięcia aktywacji, pełnego zarządzania certyfikatami i wszystkie bez dodatkowych kosztów do normalnej cennik usługi CDN.

Ta możliwość jest ważne, ponieważ pozwala na ochronę prywatności i integralność danych aplikacji sieci web poufne przesyłane. Przy użyciu protokołu SSL do obsługi ruchu za pośrednictwem protokołu HTTPS zapewnia, że dane są szyfrowane, gdy są wysyłane przez internet. HTTPS zapewnia zaufania i uwierzytelniania, a następnie chroni przed atakami aplikacji sieci web.

> [!NOTE]  
> Oprócz zapewnienia obsługi protokołu SSL dla nazwy domeny niestandardowej, usługi Azure CDN może ułatwić skalowanie aplikacji do dostarczania zawartości wysokiej przepustowości na świecie. Aby dowiedzieć się więcej, zapoznaj się [Omówienie usługi Azure CDN](../../cdn/cdn-overview.md).

## <a name="quick-start"></a>Szybki start
Poniżej przedstawiono kroki wymagane do włączenia protokołu HTTPS dla punktu końcowego magazynu niestandardowego obiektu blob:

1.  [Integracja z usługą Azure CDN konta magazynu platformy Azure](../../cdn/cdn-create-a-storage-account-with-cdn.md).
    Ten artykuł przeprowadzi Cię przez proces tworzenia konta magazynu w portalu Azure, jeśli nie zostało to jeszcze zrobione.

    > [!NOTE]  
    > Podczas udostępniania wersji zapoznawczej obsługi statycznych witryn sieci Web w usłudze Azure Storage wybierz "źródła niestandardowego" rozwijanym "typ źródła" menu, aby dodać punkt końcowy sieci web magazynu. W portalu Azure należy to zrobić z profilu CDN zamiast bezpośrednio na koncie magazynu.

2.  [Mapuj zawartość Azure CDN na domenę niestandardową](../../cdn/cdn-map-content-to-custom-domain.md).
3.  [Włącz protokół HTTPS na domenę niestandardową Azure CDN](../../cdn/cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>Sygnatury dostępu współdzielonego
Jeśli skonfigurowano punktu końcowego magazynu obiektów blob nie zezwalaj na anonimowy dostęp do odczytu, musisz podać [dostępu sygnatury dostępu Współdzielonego](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) tokenu w każdym żądaniu należy do domeny niestandardowej. Domyślnie punkty końcowe magazynu obiektów blob nie zezwalaj na anonimowy dostęp do odczytu. Zobacz [Zarządzanie anonimowy dostęp do odczytu do kontenerów i obiektów blob](storage-manage-access-to-resources.md) Aby uzyskać więcej informacji na temat sygnatur dostępu współdzielonego.

Usługi Azure CDN nie przestrzega ograniczeń dodane do tokenu sygnatury dostępu Współdzielonego. Na przykład wszystkie tokeny sygnatury dostępu Współdzielonego ma czasu wygaśnięcia. Oznacza to, że zawartość nadal będą dostępne z wygasłych SAS, aż do tej zawartości jest wyczyszczone z węzłów CDN krawędzi. Można kontrolować, jak długo dane są buforowane w sieci CDN przez ustawienie nagłówka odpowiedzi pamięci podręcznej. Zobacz [zarządzaniu wygasaniem obiektów blob magazynu Azure w usłudze Azure CDN](../../cdn/cdn-manage-expiration-of-blob-content.md) instrukcje.

W przypadku utworzenia wielu skojarzeń zabezpieczeń adresów URL dla tego samego punktu końcowego obiektu blob, zaleca się włączenie buforowania dla Twojej usługi Azure CDN ciągu zapytania. To, aby upewnić się, że każdy adres URL jest traktowany jako unikatowe jednostki. Zobacz [kontrolowanie Azure CDN buforowanie z ciągami zapytań](../../cdn/cdn-query-string.md) Aby uzyskać więcej informacji.

## <a name="http-to-https-redirection"></a>Protokół HTTP do przekierowania protokołu HTTPS
Można wybrać do przekierowywania ruchu HTTP, HTTPS. To wymaga korzystania z usługi Azure CDN premium oferty from Verizon. Musisz [zachowanie zastąpienie HTTP przy użyciu usługi Azure CDN aparatu reguł](../../cdn/cdn-rules-engine.md) z następującą regułą:

![](./media/storage-https-custom-domain-cdn/redirect-to-https.png)

"Nazwa Cdn — punktu końcowego" odwołuje się do nazwy skonfigurowanego dla punktu końcowego CDN. Tę wartość można wybrać z listy rozwijanej. "-Ścieżka do źródła" odnosi się do ścieżki w ramach konta magazynu pochodzenia, gdzie znajduje się zawartości statycznej. Jeśli przechowujesz całej zawartości statycznej w jeden kontener Zamień na nazwę kontenera "— Ścieżka do źródła".

Aby uzyskać bardziej zgłębić temat do zasad, zobacz [zasady usługi Azure CDN aparat funkcji](../../cdn/cdn-rules-engine-reference-features.md).

## <a name="pricing-and-billing"></a>Cennik i rozliczenia
Gdy uzyskujesz dostęp do obiektów blob za pośrednictwem usługi Azure CDN, płacisz [obiektu Blob magazynu ceny](https://azure.microsoft.com/pricing/details/storage/blobs/) ruchu między węzłami krawędzi i pochodzenia (magazynu obiektów Blob) i [ceny CDN](https://azure.microsoft.com/pricing/details/cdn/) dla danych dostępnych z węzłów krawędzi.

Na przykład załóżmy, że konto magazynu w zachodnie stany USA, który jest uzyskiwany za pomocą usługi Azure CDN. W UK próbie dostępu obiektów blob w tym kontem magazynu za pośrednictwem sieci CDN, Azure najpierw sprawdza węzła krawędzi najbliżej UK dla tego obiektu blob. Jeśli znaleziono, jego uzyskuje dostęp do tej kopii obiektu blob i użyje cennik usługi CDN, ponieważ jest on dostępny w sieci CDN. Jeśli nie zostanie znaleziony, Azure spowoduje skopiowanie obiektu blob z węzłem krawędzi, która powoduje wyjście i transakcji opłat, jak określono w cen magazynu obiektów Blob, a następnie dostępu do pliku na węzeł krawędzi, co spowoduje rozliczeń CDN.

Podczas przeglądania [cennik usługi CDN](https://azure.microsoft.com/pricing/details/cdn/), należy pamiętać, że obsługa protokołu HTTPS dla niestandardowych nazw domen jest dostępna tylko dla usługi Azure CDN z produktów Verizon (Standard i Premium).

## <a name="next-steps"></a>Kolejne kroki
* [Konfigurowanie niestandardowej nazwy domeny dla punktu końcowego magazynu obiektów Blob](storage-custom-domain-name.md)
* [Statyczna witryna internetowa hosting w usłudze Azure Storage (wersja zapoznawcza)](storage-blob-static-website.md)
