---
title: Konfigurowanie nazw DNS za pomocą Menedżera ruchu
description: Dowiedz się, jak skonfigurować domenę niestandardową dla aplikacji usługi Azure App Service, która integruje się z usługą Traffic Manager w celu równoważenia obciążenia.
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.topic: article
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: f8322c12669e41fc7c9aa88e99f95cf1b26ea87d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944128"
---
# <a name="configure-a-custom-domain-name-in-azure-app-service-with-traffic-manager-integration"></a>Konfigurowanie niestandardowej nazwy domeny w usłudze Azure App Service za pomocą integracji usługi Traffic Manager

[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

> [!NOTE]
> W przypadku usług w chmurze zobacz [Konfigurowanie niestandardowej nazwy domeny dla usługi w chmurze platformy Azure](../cloud-services/cloud-services-custom-domain-name.md).

Korzystając z [usługi Azure Traffic Manager](/azure/traffic-manager/) do równoważenia obciążenia ruchu do usługi Azure App [Service,](overview.md)dostęp do aplikacji Usługi App Service można uzyskać za pomocą ** \<punktu końcowego traffic-manager>.trafficmanager.net**. W aplikacji App Service można przypisać\.niestandardową nazwę domeny, taką jak www contoso.com, aby zapewnić użytkownikom bardziej rozpoznawalną nazwę domeny.

W tym artykule pokazano, jak skonfigurować niestandardową nazwę domeny za pomocą aplikacji usługi App Service zintegrowanej z [Usługą Traffic Manager](../traffic-manager/traffic-manager-overview.md).

> [!NOTE]
> Tylko rekordy [CNAME](https://en.wikipedia.org/wiki/CNAME_record) są obsługiwane podczas konfigurowania nazwy domeny przy użyciu punktu końcowego usługi Traffic Manager. Ponieważ rekordy A nie są obsługiwane, mapowanie domeny głównej, takie jak contoso.com, również nie jest obsługiwane.
> 

## <a name="prepare-the-app"></a>Przygotowywanie aplikacji

Aby zamapować niestandardową nazwę DNS na aplikację zintegrowaną z usługą Azure Traffic Manager, [plan usługi app service](https://azure.microsoft.com/pricing/details/app-service/) aplikacji sieci web musi być w **warstwie Standardowa** lub nowszej. W tym kroku musisz się upewnić, że Twoja aplikacja usługi App Service jest w obsługiwanej warstwie cenowej.

### <a name="check-the-pricing-tier"></a>Sprawdzanie warstwy cenowej

W [witrynie Azure portal](https://portal.azure.com)wyszukaj i wybierz **pozycję Usługi aplikacji**.

Na stronie **Usługi aplikacji** wybierz nazwę aplikacji platformy Azure.

![Nawigacja w portalu do aplikacji platformy Azure](./media/app-service-web-tutorial-custom-domain/select-app.png)

W lewej części strony aplikacji wybierz pozycję **Skaluj w górę (plan usługi app service).**

![Menu skalowania w górę](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Bieżąca warstwa aplikacji jest wyróżniona niebieskim obramowaniem. Sprawdź, czy aplikacja znajduje się w warstwie **Standardowa** lub wyższej (dowolna warstwa w kategorii **Produkcja** lub **Izolowane).** Jeśli tak, zamknij stronę **Skalowanie w górę** i przejdź do [przycisku Utwórz mapowanie CNAME](#create-the-cname-mapping).

![Sprawdzanie warstwy cenowej](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

### <a name="scale-up-the-app-service-plan"></a>Skalowanie w górę planu usługi App Service

Jeśli chcesz skalować aplikację, wybierz dowolną z warstw cenowych w kategorii **Produkcja.** Aby uzyskać dodatkowe opcje, kliknij pozycję **Wyświetl dodatkowe opcje**.

Kliknij przycisk **Zastosuj**.

## <a name="create-traffic-manager-endpoint"></a>Tworzenie punktu końcowego programu Traffic Manager

Wykonując kroki opisane w [witrynie Dodaj lub usuń punkty końcowe,](../traffic-manager/traffic-manager-endpoints.md)dodaj aplikację usługi App Service jako punkt końcowy w profilu usługi Traffic Manager.

Gdy aplikacja usługi App Service znajduje się w obsługiwanej warstwie cenowej, pojawia się na liście dostępnych obiektów docelowych usługi App Service po dodaniu punktu końcowego. Jeśli aplikacji nie ma na liście, [sprawdź warstwę cenową aplikacji](#prepare-the-app).

## <a name="create-the-cname-mapping"></a>Tworzenie mapowania CNAME
> [!NOTE]
> Aby skonfigurować [zakupioną domenę usługi App Service,](manage-custom-dns-buy-domain.md)pomiń tę sekcję i przejdź do [opcji Włącz domenę niestandardową](#enable-custom-domain).
> 

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

Podczas gdy specyfika każdego dostawcy domeny jest różna, mapujesz *od* niestandardowej nazwy domeny (takiej jak **contoso.com)** *do* nazwy domeny Usługi Traffic Manager **(contoso.trafficmanager.net),** która jest zintegrowana z aplikacją.

> [!NOTE]
> Jeśli rekord jest już używany i musisz wstępnie powiązać z nim aplikacje, można utworzyć dodatkowy rekord CNAME. Na przykład, aby wstępnie powiązać **contoso.com\.www** z aplikacją, utwórz rekord CNAME z **awverify.www** do **contoso.trafficmanager.net**. Następnie możesz dodać\."www contoso.com" do aplikacji bez konieczności zmiany rekordu CNAME "www". Aby uzyskać więcej informacji, zobacz [Migrowanie aktywnej nazwy DNS do usługi Azure App Service](manage-custom-dns-migrate-domain.md).

Po zakończeniu dodawania lub modyfikowania rekordów DNS u dostawcy domeny zapisz zmiany.

## <a name="enable-custom-domain"></a>Włączanie domeny niestandardowej
Po propagacji rekordów nazwy domeny użyj przeglądarki, aby sprawdzić, czy niestandardowa nazwa domeny jest rozpoznawana w aplikacji usługi App Service.

> [!NOTE]
> Może upłynąć trochę czasu, aby CNAME propagować za pośrednictwem systemu DNS. Można użyć usługi, <a href="https://www.digwebinterface.com/">https://www.digwebinterface.com/</a> takich jak sprawdzić, czy CNAME jest dostępny.
> 
> 

1. Po pomyślnym rozwiązaniu domeny wróć do strony aplikacji w witrynie [Azure Portal](https://portal.azure.com)
2. W lewej nawigacji wybierz pozycję **Domeny niestandardowe** > **Dodaj nazwę hosta**.
4. Wpisz niestandardową nazwę domeny, która została zamapowana wcześniej, i wybierz pozycję **Sprawdź poprawność**.
5. Upewnij się, że **typ rekordu nazwy hosta** jest ustawiony na **CNAME (www\.example.com lub dowolną poddomenę).**

6. Ponieważ aplikacja App Service jest teraz zintegrowana z punktem końcowym usługi Traffic Manager, w **konfiguracji CNAME**powinna zostać wyświetlone nazwa domeny usługi Traffic Manager. Zaznacz go i kliknij pozycję **Dodaj domenę niestandardową**.

    ![Dodawanie nazwy DNS do aplikacji](./media/configure-domain-traffic-manager/enable-traffic-manager-domain.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zabezpieczanie niestandardowej nazwy DNS przy użyciu powiązania SSL w usłudze Azure App Service](configure-ssl-bindings.md)
