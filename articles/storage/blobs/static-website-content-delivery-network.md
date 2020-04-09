---
title: Integracja statycznej witryny sieci Web z usługą Azure CDN — Usługa Azure Storage
description: Dowiedz się, jak buforować zawartość statycznej witryny sieci Web z konta usługi Azure Storage przy użyciu usługi Azure Content Delivery Network (CDN).
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 04/07/2020
ms.openlocfilehash: 4516e9f48174a0f1f5201c46cf114badf13d99d6
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878825"
---
# <a name="integrate-a-static-website-with-azure-cdn"></a>Integracja statycznej witryny sieci Web z usługą Azure CDN

Można włączyć [usługę Azure Content Delivery Network (CDN)](../../cdn/cdn-overview.md) do buforowania zawartości ze [statycznej witryny sieci Web,](storage-blob-static-website.md) która jest hostowana na koncie magazynu platformy Azure. Za pomocą usługi Azure CDN można skonfigurować niestandardowy punkt końcowy domeny dla statycznej witryny sieci Web, aprowizować niestandardowe certyfikaty TLS/SSL i skonfigurować niestandardowe reguły przepisywania. Konfigurowanie usługi Azure CDN oznacza dodatkowe koszty, ale zapewnia stale niskie opóźnienia połączenia z witryną internetowej z dowolnego miejsca na świecie. Usługa Azure CDN zapewnia również szyfrowanie TLS z własnym certyfikatem. 

Aby uzyskać dodatkowe informacje na temat cennika usługi Azure CDN, zobacz [Cennik usługi Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="enable-azure-cdn-for-your-static-website"></a>Włącz usługę Azure CDN dla swojej statycznej witryny sieci Web

Można włączyć usługę Azure CDN dla statycznej witryny sieci Web bezpośrednio z konta magazynu. wysJeśli chcesz określić zaawansowane ustawienia konfiguracji dla punktu końcowego usługi CDN, takie jak [optymalizacja pobierania dużych plików](../../cdn/cdn-optimization-overview.md#large-file-download), możesz zamiast tego użyć [rozszerzenia usługi Azure CDN](../../cdn/cdn-create-new-endpoint.md), aby utworzyć punkt końcowy i profil usługi CDN.

1. Znajdź swoje konto magazynu w witrynie Azure portal i wyświetl omówienie konta.

1. W menu **Usługa obiektów blob** wybierz pozycję **Azure CDN,** aby otworzyć stronę **usługi Azure CDN:**

    ![Tworzenie punktu końcowego usługi CDN](media/storage-blob-static-website-custom-domain/cdn-storage-new.png)

1. W sekcji **profilu sieci CDN** określ, czy utworzyć nowy profil sieci CDN, czy użyć istniejącego. Profil sieci CDN to zbiór punktów końcowych usługi CDN, które współużytkują warstwę cenową i dostawcę. Następnie wprowadź nazwę sieci CDN, która jest unikatowa w ramach subskrypcji.

1. Określ warstwę cenową dla punktu końcowego sieci CDN. Aby dowiedzieć się więcej o cenach, zobacz [Cennik sieci dostarczania zawartości](https://azure.microsoft.com/pricing/details/cdn/). Aby uzyskać więcej informacji na temat funkcji dostępnych dla każdej warstwy, zobacz [Porównanie funkcji produktu usługi Azure CDN](../../cdn/cdn-features.md).

1. W polu **nazwa punktu końcowego sieci CDN** określ nazwę punktu końcowego sieci CDN. Punkt końcowy usługi CDN musi być unikatowy na platformie Azure i zawiera pierwszą część adresu URL punktu końcowego. Formularz sprawdza, czy nazwa punktu końcowego jest unikatowa.

1. Określ statyczny punkt końcowy witryny sieci Web w polu **Nazwa hosta pochodzenia.** 

   Aby znaleźć statyczny punkt końcowy witryny sieci Web, przejdź do ustawień **statycznej witryny sieci Web** dla konta magazynu.  Skopiuj podstawowy punkt końcowy i wklej go do konfiguracji sieci CDN.

   > [!IMPORTANT]
   > Upewnij się, że usuniesz identyfikator protokołu *(np.* https) i końcowe ukośnik w adresie URL. Na przykład, jeśli statyczny punkt `https://mystorageaccount.z5.web.core.windows.net/`końcowy witryny `mystorageaccount.z5.web.core.windows.net` sieci Web jest , a następnie należy określić w **polu Nazwa hosta pochodzenia.**

   Na poniższej ilustracji przedstawiono przykładową konfigurację punktu końcowego:

   ![Zrzut ekranu przedstawiający przykładową konfigurację punktu końcowego sieci CDN](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. Wybierz **pozycję Utwórz**, a następnie poczekaj, aż sieć CDN zostanie udostępnina. Po utworzeniu punktu końcowego zostanie on wyświetlony na liście punktów końcowych. (Jeśli w formularzu są jakieś błędy, obok tego pola pojawi się wykrzyknik).

1. Aby sprawdzić, czy punkt końcowy sieci CDN jest poprawnie skonfigurowany, kliknij punkt końcowy, aby przejść do jego ustawień. Z omówienia sieci CDN dla konta magazynu znajdź nazwy hosta punktu końcowego i przejdź do punktu końcowego, jak pokazano na poniższej ilustracji. Format punktu końcowego sieci CDN `https://staticwebsitesamples.azureedge.net`będzie podobny do .

    ![Zrzut ekranu przedstawiający przegląd punktu końcowego sieci CDN](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

1. Po zainicjowaniu obsługi administracyjnej punktu końcowego sieci CDN nawigowanie do punktu końcowego sieci CDN powoduje wyświetlenie zawartości pliku index.html, który został wcześniej przekazany do statycznej witryny sieci Web.

1. Aby przejrzeć ustawienia pochodzenia punktu końcowego sieci CDN, przejdź do **punktu początkowego w** sekcji **Ustawienia** dla punktu końcowego sieci CDN. Zobaczysz, że pole **typu Pochodzenie** jest ustawione na *Niestandardowe pochodzenie* i że pole nazwa **hosta pochodzenia** wyświetla statyczny punkt końcowy witryny sieci Web.

    ![Zrzut ekranu przedstawiający ustawienia pochodzenia punktu końcowego sieci CDN](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="remove-content-from-azure-cdn"></a>Usuwanie zawartości z usługi Azure CDN

Jeśli nie chcesz już buforować obiektu w usłudze Azure CDN, możesz wykonać jedną z następujących czynności:

* Ustaw kontener jako prywatny, a nie publiczny. Aby uzyskać więcej informacji, zobacz [Zarządzanie anonimowym dostępem do odczytu kontenerów i obiektów blob.](storage-manage-access-to-resources.md)
* Wyłącz lub usuń punkt końcowy usługi CDN przy użyciu witryny Azure Portal.
* Zmodyfikuj usługę hostowaną w taki sposób, aby nie odpowiadała już na żądania dla obiektu.

Obiekt, który jest już buforowany w usłudze Azure CDN, pozostaje w pamięci podręcznej, dopóki nie zakończy się okres czasu wygaśnięcia dla obiektu lub dopóki punkt końcowy nie zostanie [przeczyszczony](../../cdn/cdn-purge-endpoint.md). Po zakończeniu okresu czasu wygaśnięcia usługa Azure CDN określa, czy punkt końcowy usługi CDN jest nadal ważny i czy obiekt jest nadal anonimowo dostępny. Jeżeli tak nie jest, obiekt nie będzie już buforowany.

## <a name="next-steps"></a>Następne kroki

(Opcjonalnie) Dodaj domenę niestandardową do punktu końcowego usługi Azure CDN. Zobacz [Samouczek: Dodawanie domeny niestandardowej do punktu końcowego usługi Azure CDN](../../cdn/cdn-map-content-to-custom-domain.md).
