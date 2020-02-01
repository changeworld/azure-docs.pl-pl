---
title: Integrowanie statycznej witryny internetowej z usługą Azure CDN Storage
description: Dowiedz się, jak buforować zawartość statycznej witryny internetowej z konta usługi Azure Storage za pomocą usługi Azure Content Delivery Network (CDN).
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 01/22/2020
ms.openlocfilehash: aaf61ccbb3577036c614aa6196d2af57124550fa
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76908555"
---
# <a name="integrate-a-static-website-with-azure-cdn"></a>Integrowanie statycznej witryny sieci Web z Azure CDN

Możesz włączyć [usługę Azure Content Delivery Network (CDN)](../../cdn/cdn-overview.md) w celu buforowania zawartości z [statycznej witryny sieci Web](storage-blob-static-website.md) , która jest hostowana na koncie usługi Azure Storage. Za pomocą Azure CDN można skonfigurować niestandardowy punkt końcowy domeny dla statycznej witryny sieci Web, udostępnić niestandardowe certyfikaty SSL i skonfigurować niestandardowe reguły ponownego zapisywania. Konfigurowanie usługi Azure CDN oznacza dodatkowe koszty, ale zapewnia stale niskie opóźnienia połączenia z witryną internetowej z dowolnego miejsca na świecie. Usługa Azure CDN umożliwia również szyfrowanie za pomocą protokołu SSL z użyciem własnego certyfikatu. 

Aby uzyskać dodatkowe informacje na temat cennika usługi Azure CDN, zobacz [Cennik usługi Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="enable-azure-cdn-for-your-static-website"></a>Włącz Azure CDN dla statycznej witryny sieci Web

Możesz włączyć Azure CDN dla statycznej witryny sieci Web bezpośrednio z konta magazynu. wysJeśli chcesz określić zaawansowane ustawienia konfiguracji dla punktu końcowego usługi CDN, takie jak [optymalizacja pobierania dużych plików](../../cdn/cdn-optimization-overview.md#large-file-download), możesz zamiast tego użyć [rozszerzenia usługi Azure CDN](../../cdn/cdn-create-new-endpoint.md), aby utworzyć punkt końcowy i profil usługi CDN.

1. Znajdź konto magazynu w Azure Portal i Wyświetl przegląd konta.

2. Wybierz pozycję **Azure CDN** w menu **Blob Service**, aby skonfigurować usługę Azure CDN.

    Zostanie wyświetlona strona **Azure CDN**.

    ![Tworzenie punktu końcowego usługi CDN](../../cdn/media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-creation.png)

3. W sekcji **profil usługi CDN** określ nowy lub istniejący profil usługi CDN. 

4. Określ warstwę cenową punktu końcowego usługi CDN. Aby dowiedzieć się więcej o cenach, zobacz [cennik Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/). Aby uzyskać więcej informacji o funkcjach dostępnych dla każdej warstwy, zobacz [Compare Azure CDN Product Features](../../cdn/cdn-features.md).

5. W polu **Nazwa punktu końcowego usługi CDN** Podaj nazwę punktu końcowego sieci CDN. Punkt końcowy usługi CDN musi być unikatowy na platformie Azure.

6. Określ, że jesteś statycznym punktem końcowym witryny sieci Web w polu **Nazwa hosta źródła** . 

   Aby znaleźć punkt końcowy statycznej witryny internetowej, przejdź do ustawień **statycznej witryny internetowej** dla konta magazynu.  Skopiuj podstawowy punkt końcowy i wklej go do konfiguracji sieci CDN.

   > [!IMPORTANT]
   > Pamiętaj o usunięciu identyfikatora protokołu (*np.* https) oraz końcowego ukośnika w adresie URL. Na przykład jeśli punkt końcowy statycznej witryny sieci Web jest `https://mystorageaccount.z5.web.core.windows.net/`, należy określić `mystorageaccount.z5.web.core.windows.net` w polu **Nazwa hosta źródła** .

   Na poniższej ilustracji przedstawiono przykładową konfigurację punktu końcowego:

   ![Zrzut ekranu przedstawiający przykładową konfigurację punktu końcowego usługi CDN](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

7. Wybierz pozycję **Utwórz**, a następnie poczekaj na jej propagowanie. Po utworzeniu punktu końcowego zostanie on wyświetlony na liście punktów końcowych.

8. Aby sprawdzić, czy punkt końcowy usługi CDN został prawidłowo skonfigurowany, kliknij punkt końcowy, aby przejść do jego ustawień. W obszarze przegląd usługi CDN dla konta magazynu Znajdź nazwę hosta punktu końcowego i przejdź do punktu końcowego, jak pokazano na poniższej ilustracji. Format punktu końcowego usługi CDN będzie podobny do `https://staticwebsitesamples.azureedge.net`.

    ![Zrzut ekranu przedstawiający przegląd punktu końcowego usługi CDN](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

9. Po zakończeniu propagacji punktu końcowego usługi CDN przechodzenie do punktu końcowego usługi CDN spowoduje wyświetlenie zawartości pliku index. html, który został wcześniej przekazany do statycznej witryny sieci Web.

10. Aby przejrzeć ustawienia źródła dla punktu końcowego usługi CDN, przejdź do lokalizacji **źródłowej** w sekcji **Ustawienia** dla punktu końcowego usługi CDN. Zobaczysz, że w polu **Typ źródła** jest ustawiona wartość *Źródło niestandardowe* i że w polu **Nazwa hosta źródła** jest wyświetlany statyczny punkt końcowy witryny sieci Web.

    ![Zrzut ekranu przedstawiający ustawienia źródła dla punktu końcowego usługi CDN](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="remove-content-from-azure-cdn"></a>Usuwanie zawartości z usługi Azure CDN

Jeśli nie chcesz już buforować obiektu w usłudze Azure CDN, możesz wykonać jedną z następujących czynności:

* Ustaw kontener jako prywatny, a nie publiczny. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem anonimowym w trybie odczytu do kontenerów i obiektów blob](storage-manage-access-to-resources.md).
* Wyłącz lub usuń punkt końcowy usługi CDN przy użyciu witryny Azure Portal.
* Zmodyfikuj usługę hostowaną w taki sposób, aby nie odpowiadała już na żądania dla obiektu.

Obiekt, który jest już buforowany w usłudze Azure CDN, pozostaje w pamięci podręcznej, dopóki nie zakończy się okres czasu wygaśnięcia dla obiektu lub dopóki punkt końcowy nie zostanie [przeczyszczony](../../cdn/cdn-purge-endpoint.md). Po zakończeniu okresu czasu wygaśnięcia usługa Azure CDN określa, czy punkt końcowy usługi CDN jest nadal ważny i czy obiekt jest nadal anonimowo dostępny. Jeżeli tak nie jest, obiekt nie będzie już buforowany.

## <a name="next-steps"></a>Następne kroki

Obowiązkowe Dodaj domenę niestandardową do punktu końcowego Azure CDN. Zobacz [Samouczek: Dodawanie domeny niestandardowej do punktu końcowego Azure CDN](../../cdn/cdn-map-content-to-custom-domain.md).
