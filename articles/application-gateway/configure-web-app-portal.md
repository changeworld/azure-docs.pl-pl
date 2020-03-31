---
title: Zarządzanie ruchem do aplikacji z wieloma dzierżawami za pomocą portalu
titleSuffix: Azure Application Gateway
description: Ten artykuł zawiera wskazówki dotyczące konfigurowania aplikacji sieci web usługi Azure App jako członków w puli wewnętrznej bazy danych w istniejącej lub nowej bramie aplikacji.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 0ec417b3c7a025d2d05bdd74ec683a2891c3b0de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74075159"
---
# <a name="configure-app-service-with-application-gateway"></a>Konfigurowanie usługi App Service przy zastosowaniu bramy aplikacji

Ponieważ usługa aplikacji jest usługą wielodostępną zamiast wdrożenia dedykacji, używa nagłówka hosta w żądaniu przychodzącym, aby rozwiązać żądanie do punktu końcowego usługi aplikacji poprawne. Zazwyczaj nazwa DNS aplikacji, która z kolei jest nazwą DNS skojarzoną z bramą aplikacji, która prowadzi do usługi aplikacji, różni się od nazwy domeny usługi aplikacji wewnętrznej bazy danych. W związku z tym nagłówek hosta w oryginalnym żądaniu odebranym przez bramę aplikacji nie jest taki sam jak nazwa hosta usługi wewnętrznej bazy danych. Z tego powodu, chyba że nagłówek hosta w żądaniu z bramy aplikacji do wewnętrznej bazy danych zostanie zmieniona na nazwę hosta usługi wewnętrznej bazy danych, zaplecze wielu dzierżawców nie są w stanie rozpoznać żądania do prawidłowego punktu końcowego.

Brama aplikacji udostępnia `Pick host name from backend address` przełącznik wywołany, który zastępuje nagłówek hosta w żądaniu nazwą hosta zaplecza, gdy żądanie jest kierowane z bramy aplikacji do wewnętrznej bazy danych. Ta funkcja umożliwia obsługę zaplecza wielu dzierżawców, takich jak usługa aplikacji platformy Azure i zarządzanie interfejsami API. 

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
>
> - Tworzenie puli wewnętrznej bazy danych i dodawanie do niej usługi app service
> - Tworzenie ustawień HTTP i sondy niestandardowej z włączonymi przełącznikami "Wybierz nazwy hosta"

## <a name="prerequisites"></a>Wymagania wstępne

- Brama aplikacji: Jeśli nie masz istniejącej bramy aplikacji, zobacz, jak [utworzyć bramę aplikacji](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
- Usługa aplikacji: jeśli nie masz istniejącej usługi aplikacji, zobacz [dokumentację usługi aplikacji](https://docs.microsoft.com/azure/app-service/).

## <a name="add-app-service-as-backend-pool"></a>Dodawanie usługi aplikacji jako puli wewnętrznej bazy danych

1. W witrynie Azure portal otwórz widok konfiguracji bramy aplikacji.

2. W obszarze **Pule wewnętrznej bazy**danych kliknij przycisk **Dodaj,** aby utworzyć nową pulę zaplecza.

3. Podaj odpowiednią nazwę do puli wewnętrznej bazy danych. 

4. W obszarze **Obiekty docelowe**kliknij na rozwijanie i wybierz **app services** jako opcję.

5. Pojawi się lista rozwijana bezpośrednio pod listą rozwijaną **Cele,** która będzie zawierać listę usług aplikacji. Z tej listy rozwijanej wybierz usługę aplikacji, którą chcesz dodać jako członka puli wewnętrznej bazy danych, a następnie kliknij przycisk Dodaj.

   ![Zaplecze usługi aplikacji](./media/configure-web-app-portal/backendpool.png)
   
   > [!NOTE]
   > Listy rozwijanej wypełni tylko te usługi aplikacji, które są w tej samej subskrypcji co brama aplikacji. Jeśli chcesz użyć usługi aplikacji, która jest w innej subskrypcji niż ta, w której jest brama aplikacji, zamiast wybierać **usługi aplikacji** w rozwijaniu **Cele,** wybierz **opcję adres IP lub nazwa hosta** i wprowadź nazwa hosta (przykład). azurewebsites.net) usługi aplikacji.

## <a name="create-http-settings-for-app-service"></a>Tworzenie ustawień HTTP dla usługi app

1. W obszarze **Ustawienia HTTP**kliknij pozycję **Dodaj,** aby utworzyć nowe ustawienie HTTP.

2. Wprowadź nazwę ustawienia HTTP i możesz włączyć lub wyłączyć koligacji opartej na plikach cookie zgodnie z wymaganiami.

3. Wybierz protokół jako HTTP lub HTTPS zgodnie z przypadkiem użycia. 

   > [!NOTE]
   > Jeśli wybierzesz protokół HTTPS, nie trzeba przekazywać żadnego certyfikatu uwierzytelniania ani zaufanego certyfikatu głównego, aby wpisać na białą listę zaplecza usługi aplikacji, ponieważ usługa aplikacji jest zaufaną usługą platformy Azure.

4. Zaznacz pole **wyboru Użyj dla usługi app service** . Należy pamiętać, `Create a probe with pick host name from backend address` że `Pick host name from backend address` przełączniki i automatycznie się włączyć.`Pick host name from backend address` zastąpi nagłówek hosta w żądaniu nazwą hosta zaplecza, gdy żądanie jest kierowane z bramy aplikacji do wewnętrznej bazy danych.  

   `Create a probe with pick host name from backend address`automatycznie utworzy sondę kondycji i skojarzy ją z tym ustawieniem HTTP. Nie trzeba tworzyć żadnych innych sondy kondycji dla tego ustawienia HTTP. Można sprawdzić, czy nowa sonda o nazwie <HTTP Setting name> <Unique GUID> została dodana na liście sond `Pick host name from backend http settings enabled`kondycji i ma już przełącznik .

   Jeśli masz już jedno lub więcej ustawień HTTP, które są używane w usłudze App Service i jeśli te ustawienia HTTP używają `Create a probe with pick host name from backend address` tego samego protokołu, który używasz w tym, którego tworzysz, zamiast przełącznika, otrzymasz rozwijaną, aby wybrać jedną z niestandardowych sond . Dzieje się tak dlatego, że istnieje już ustawienie HTTP z usługą aplikacji, w `Pick host name from backend http settings enabled` związku z tym istnieje również sonda kondycji, która ma przełącznik . Wybierz tę niestandardową sondę z listy rozwijanej.

5. Kliknij **przycisk OK,** aby utworzyć ustawienie HTTP.

   ![Ustawienie HTTP1](./media/configure-web-app-portal/http-setting1.png)

   ![Ustawienie HTTP2](./media/configure-web-app-portal/http-setting2.png)



## <a name="create-rule-to-tie-the-listener-backend-pool-and-http-setting"></a>Tworzenie reguły w celu powiązania odbiornika, puli zaplecza i ustawienia HTTP

1. W obszarze **Reguły**kliknij pozycję **Podstawowe,** aby utworzyć nową regułę podstawową.

2. Podaj odpowiednią nazwę i wybierz odbiornik, który będzie akceptował przychodzące żądania dla usługi App.

3. W **puli wewnętrznej bazy danych** wybierz pulę zaplecza utworzoną powyżej.

4. Z **listy** rozwijanej ustawień HTTP wybierz ustawienie HTTP utworzone powyżej.

5. Kliknij **przycisk OK,** aby zapisać tę regułę.

   ![Reguła](./media/configure-web-app-portal/rule.png)

## <a name="additional-configuration-in-case-of-redirection-to-app-services-relative-path"></a>Dodatkowa konfiguracja w przypadku przekierowania do ścieżki względnej usługi aplikacji

Gdy usługa aplikacji wysyła odpowiedź przekierowania do klienta, aby przekierować do jego ścieżki względnej (Na przykład przekierowanie z contoso.azurewebsites.net/path1 do contoso.azurewebsites.net/path2), używa tej samej nazwy hosta w nagłówku lokalizacji jego odpowiedzi jako w żądaniu odebranym z bramy aplikacji. Dlatego klient będzie żądanie bezpośrednio do contoso.azurewebsites.net/path2 zamiast przechodzić przez bramę aplikacji (contoso.com/path2). Pomijanie bramy aplikacji nie jest pożądane.

Jeśli w przypadku użycia istnieją scenariusze, w których usługa app będzie musiała wysłać odpowiedź przekierowania do klienta, wykonaj [dodatkowe kroki, aby przepisać nagłówek lokalizacji](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url#sample-configuration).

## <a name="restrict-access"></a>Ograniczanie dostępu

Aplikacje sieci web wdrożone w tych przykładach używają publicznych adresów IP, do których można uzyskać dostęp bezpośrednio z Internetu. Pomaga to w rozwiązywaniu problemów podczas uczenia się o nowej funkcji i wypróbowywania nowych rzeczy. Jeśli jednak zamierzasz wdrożyć funkcję w produkcji, należy dodać więcej ograniczeń.

Jednym ze sposobów ograniczenia dostępu do aplikacji internetowych jest użycie [statycznych ograniczeń IP usługi Azure App Service.](../app-service/app-service-ip-restrictions.md) Na przykład można ograniczyć aplikację sieci web, tak aby odbierała tylko ruch z bramy aplikacji. Funkcja ograniczenia IP usługi aplikacji służy do listy adresów VIP bramy aplikacji jako jedynego adresu z dostępem.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o usłudze aplikacji i innych usługach pomocy technicznej dla wielu dzierżaw z bramą aplikacji, zobacz [obsługa usług dla wielu dzierżaw z bramą aplikacji](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview).
