---
title: Tworzenie punktu końcowego usługi Azure CDN | Dokumenty firmy Microsoft
description: W tym artykule pokazano, jak utworzyć nowy punkt końcowy sieci dostarczania zawartości platformy Azure (CDN), w tym ustawienia zaawansowane.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: afd9599dd1e396dea6975c397688e4b97371fd6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593934"
---
# <a name="create-an-azure-cdn-endpoint"></a>Tworzenie punktu końcowego usługi Azure CDN
W tym artykule opisano wszystkie ustawienia tworzenia punktu końcowego [sieci dostarczania zawartości platformy Azure (CDN)](cdn-overview.md) w istniejącym profilu sieci CDN. Po utworzeniu profilu i punktu końcowego można rozpocząć dostarczanie zawartości do klientów. Aby uzyskać przewodnik Szybki start dotyczący tworzenia profilu i punktu końcowego, zobacz [Szybki start: Tworzenie profilu i punktu końcowego usługi Azure CDN.](cdn-create-new-endpoint.md)

## <a name="prerequisites"></a>Wymagania wstępne
Aby można było utworzyć punkt końcowy sieci CDN, należy utworzyć co najmniej jeden profil sieci CDN, który może zawierać jeden lub więcej punktów końcowych sieci CDN. Do organizowania punktów końcowych usługi CDN według domeny internetowej, aplikacji internetowej lub innych kryteriów można używać wielu profilów. Ponieważ ceny usługi CDN są stosowane na poziomie profilu sieci CDN, należy utworzyć wiele profilów sieci CDN, jeśli chcesz użyć kombinacji warstw cenowych usługi Azure CDN. Aby utworzyć profil sieci CDN, zobacz [Tworzenie nowego profilu sieci CDN](cdn-create-new-endpoint.md#create-a-new-cdn-profile).

## <a name="log-in-to-the-azure-portal"></a>Logowanie do witryny Azure Portal
Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu konta platformy Azure.

## <a name="create-a-new-cdn-endpoint"></a>Tworzenie nowego punktu końcowego usługi CDN

1. W witrynie [Azure Portal](https://portal.azure.com) przejdź do swojego profilu CDN. Być może został on przypięty do pulpitu nawigacyjnego w poprzednim kroku. Jeśli nie, możesz go znaleźć, wybierając kolejno pozycje **Wszystkie usługi** i **Profile CDN**. W okienku **Profile CDN** wybierz profil, do którego planujesz dodać punkt końcowy. 
   
    Zostanie wyświetlone okienko profilu CDN.

2. Wybierz **punkt końcowy**.
   
    ![Wybór punktu końcowego w sieci CDN](./media/cdn-create-endpoint-how-to/cdn-select-endpoint.png)
   
    Zostanie wyświetlona strona **Dodawanie punktu końcowego**.
   
    ![Dodawanie strony punktu końcowego](./media/cdn-create-endpoint-how-to/cdn-add-endpoint-page.png)

3. W polu **Nazwa** wprowadź unikatową nazwę nowego punktu końcowego CDN. Ta nazwa jest używana do uzyskiwania dostępu do buforowanych zasobów w>azureedge.net _ \<punktu końcowego _domeny.

4. W przypadku **typu origin**wybierz jeden z następujących typów początku układu współrzędnych: 
   - **Magazyn** dla usługi Azure Storage
   - **Usługa w chmurze** dla usług w chmurze platformy Azure
   - **Aplikacja Web App** dla aplikacji Azure Web Apps
   - **Niestandardowe pochodzenie** dla dowolnego innego publicznie dostępnego serwera sieci web pochodzenia (hostowanego na platformie Azure lub w innym miejscu)

5. W przypadku **nazwy hosta Origin**wybierz lub wprowadź domenę serwera pochodzenia. Lista rozwijana zawiera listę wszystkich dostępnych serwerów pochodzenia typu określonego w kroku 4. Jeśli jako typ źródła pochodzenia wybrano **opcję Niestandardowe pochodzenie,** wprowadź domenę niestandardowego serwera pochodzenia.
    
6. W przypadku **ścieżki pochodzenia**wprowadź ścieżkę do zasobów, które chcesz buforować. Aby zezwolić na buforowanie dowolnego zasobu w domenie określonej w kroku 5, pozostaw to ustawienie puste.
    
7. W polu **Nagłówek hosta źródła** wprowadź nagłówek hosta, który usługa Azure CDN ma wysłać z każdym żądaniem, lub pozostaw wartość domyślną.
   
   > [!NOTE]
   > Niektóre typy źródeł, takie jak usługi Azure Storage i Web Apps, wymagają, aby nagłówek hosta był zgodny z domeną źródła. Pozostaw wartość domyślną, chyba że masz źródło, które wymaga nagłówka hosta innego niż jego domena.
   > 
    
8. W przypadku portu **Protokołu** i **pochodzenia**określ protokoły i porty używane do uzyskiwania dostępu do zasobów na serwerze pochodzenia. Należy wybrać co najmniej jeden protokół (HTTP lub HTTPS). Użyj domeny dostarczonej przez sieć CDN_\<(>azureedge.net _punktu końcowego), aby uzyskać dostęp do zawartości HTTPS. 
   
   > [!NOTE]
   > Wartość **portu Pochodzenia** określa tylko port, którego punkt końcowy używa do pobierania informacji z serwera pochodzenia. Sam punkt końcowy jest dostępny dla klientów końcowych tylko na domyślnych portach HTTP i HTTPS (80 i 443), niezależnie od wartości **Port źródła**.  
   > 
   > Punkty końcowe w profilach usługi **Azure CDN from Akamai** nie zezwalają na pełny zakres portów TCP dla portów źródeł. Lista niedozwolonych portów źródłowych znajduje się w artykule [Azure CDN from Akamai Allowed Origin Ports](/previous-versions/azure/mt757337(v=azure.100)) (Azure CDN from Akamai — dozwolone porty źródłowe).  
   > 
   > Obsługa protokołu HTTPS dla domen niestandardowych usługi Azure CDN nie jest obsługiwana w **usłudze Azure CDN z produktów Akamai.** Aby uzyskać więcej informacji, zobacz [Konfigurowanie protokołu HTTPS w domenie niestandardowej usługi Azure CDN](cdn-custom-ssl.md).
    
9. W obszarze **Zoptymalizowane pod kątem**wybierz typ optymalizacji, który najlepiej odpowiada scenariuszowi i typowi zawartości, który ma być dostarczany przez punkt końcowy. Aby uzyskać więcej informacji, zobacz [Optymalizowanie usługi Azure CDN pod kątem typu dostarczania zawartości](cdn-optimization-overview.md).

    Obsługiwane są następujące ustawienia typu optymalizacji, w zależności od typu profilu:
    - **Standard usługi Azure CDN z** profili firmy Microsoft:
       - [**Ogólne dostarczanie w sieci Web**](cdn-optimization-overview.md#general-web-delivery)

    - **Usługa Azure CDN Standard firmy Verizon** i **Azure CDN Premium z** profili Verizon:
       - [**Ogólne dostarczanie w sieci Web**](cdn-optimization-overview.md#general-web-delivery)
       - [**Dynamiczne przyspieszanie witryny**](cdn-optimization-overview.md#dynamic-site-acceleration)

    - **Standard usługi Azure CDN z profili Akamai:**
       - [**Ogólne dostarczanie w sieci Web**](cdn-optimization-overview.md#general-web-delivery)
       - [**Ogólne przesyłanie strumieniowe multimediów**](cdn-optimization-overview.md#general-media-streaming)
       - [**Przesyłanie strumieniowe multimediów wideo na żądanie**](cdn-optimization-overview.md#video-on-demand-media-streaming)
       - [**Pobieranie dużych plików**](cdn-optimization-overview.md#large-file-download)
       - [**Dynamiczne przyspieszanie witryny**](cdn-optimization-overview.md#dynamic-site-acceleration)

10. Wybierz pozycję **Dodaj**, aby utworzyć nowy punkt końcowy.
   
    Po utworzeniu punktu końcowego zostanie on wyświetlony na liście punktów końcowych dla profilu.
    
    ![Punkt końcowy usługi CDN](./media/cdn-create-new-endpoint/cdn-endpoint-success.png)
    
    Punkt końcowy nie jest natychmiast dostępny do użycia, ponieważ propagacja rejestracji zajmuje trochę czasu: 
    - W przypadku profili usługi **Azure CDN Standard from Microsoft** propagacja zwykle trwa do 10 minut. 
    - W przypadku profili usługi **Azure CDN Standard from Akamai** propagacja zwykle trwa mniej niż jedną minutę. 
    - W przypadku profili usługi **Azure CDN Standard from Verizon** oraz usługi **Azure CDN Premium from Verizon** propagacja zwykle trwa do 90 minut. 
   
    W przypadku próby użycia nazwy domeny sieci CDN przed propagacją konfiguracji punktu końcowego do serwerów punktu obecności (POP), może zostać wyświetlony stan odpowiedzi HTTP 404. Jeśli od utworzenia punktu końcowego minęło kilka godzin i nadal otrzymujesz stan odpowiedzi 404, zobacz [Rozwiązywanie problemów z punktami końcowymi usługi Azure CDN zwracającymi kod stanu 404](cdn-troubleshoot-endpoint.md).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Aby usunąć punkt końcowy, gdy nie jest już potrzebny, zaznacz go, a następnie wybierz pozycję **Usuń**. 

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o domenach niestandardowych, przejdź do samouczka dodawania domeny niestandardowej do punktu końcowego usługi CDN.

> [!div class="nextstepaction"]
> [Dodawanie domeny niestandardowej](cdn-map-content-to-custom-domain.md)


