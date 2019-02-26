---
title: Rozwiązywanie problemów z bramą aplikacji platformy Azure z usługą App Service — przekierowania do adresu URL usługi App Service
description: Ten artykuł zawiera informacje na temat sposobu rozwiązania problemu przekierowania stosowania usługi Azure Application Gateway w usłudze Azure App Service
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/22/2019
ms.author: absha
ms.openlocfilehash: 9874ff7fde049c4dba4efb77ff541c80e462671a
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2019
ms.locfileid: "56808666"
---
# <a name="troubleshoot-application-gateway-with-app-service--redirection-to-app-services-url"></a>Rozwiązywanie problemów z bramy aplikacji przy użyciu usługi App Service — przekierowania do adresu URL usługi App Service

 Dowiedz się, jak Diagnozuj i rozwiązuj problemy przekierowania z usługą Application Gateway, w której jest wprowadzenie udostępniany adres URL usługi App Service.

## <a name="overview"></a>Przegląd

Gdy skonfigurujesz publiczny połączonego z usługi App Service w puli zaplecza bramy aplikacji, a w przypadku przekierowania skonfigurowane w kodzie aplikacji może zobaczysz, że gdy uzyskujesz dostęp do bramy aplikacji, nastąpi przekierowanie przez przeglądarkę bezpośrednio do aplikacji Adres URL usługi.

Ten problem może się zdarzyć z następujących głównych przyczyn:

- Masz przekierowania skonfigurowane w usłudze App Service. Przekierowywanie można tak proste, jak dodawanie znaku ukośnika na końcu żądania.
- Masz uwierzytelniania usługi Azure AD, co powoduje, że przekierowanie.
- Włączono przełącznik "Wybierz nazwę hosta z Address wewnętrznej bazy danych" w ustawieniach protokołu HTTP bramy Application Gateway.
- Nie masz domenę niestandardową, zarejestrowane przy użyciu usługi App Service.

## <a name="sample-configuration"></a>Przykładowa konfiguracja

- Odbiornik HTTP: Podstawowy lub połączenia obejmujące wiele lokacji
- Pula adresów zaplecza: App Service
- Ustawienia HTTP: "Wybierz nazwę hosta z adresu wewnętrznej bazy danych" włączone
- Sonda: "Wybierz nazwę hosta z ustawień protokołu HTTP" włączone

## <a name="cause"></a>Przyczyna

Usługa App Service może zostać oceniony jedynie przy użyciu skonfigurowanej nazwy hostów w ustawieniach domenę niestandardową, domyślnie, jest "example.azurewebsites.net" i ma dostęp do usługi App Service przy użyciu bramy aplikacji przy użyciu nazwy hosta nie zostanie zarejestrowane w usłudze App Service lub za pomocą Usługa Application Gateway pełni kwalifikowaną nazwę domeny, musisz zastąpić nazwę hosta w żądaniu oryginalnej nazwy hosta usługi App Service.

Aby to osiągnąć z usługą Application Gateway, możemy użyć przełącznika "Wybierz nazwę hosta z wewnętrznej bazy danych Address" w ustawieniach protokołu HTTP i sondowania do pracy, używamy "Wybierz nazwę hosta z ustawienia HTTP zaplecza" w konfiguracji sondowania.

![appservice-1](.\media\troubleshoot-app-service-redirection-app-service-url\appservice-1.png)

Ze względu na to gdy usługi App Service jest przekierowanie, używa nazwy hosta "example.azurewebsites.net" w nagłówku Location zamiast oryginalna nazwa hosta, chyba że skonfigurowano inaczej. Można sprawdzić na przykład nagłówki żądania i odpowiedzi poniżej.

Nagłówki żądania do usługi Application Gateway:

Adres URL żądania: http://www.contoso.com/path

Metoda żądania: GET

Host: www.contoso.com

Nagłówki odpowiedzi:

Kod stanu: 301 trwale przeniesiona

Lokalizacja: http://example.azurewebsites.net/path/

Serwer: Microsoft-IIS/10.0

Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=example.azurewebsites.net

X obsługiwane przez: ASP.NET

W powyższym przykładzie można zauważyć, że nagłówek odpowiedzi zawiera kod stanu 301 przekierowania i nagłówek location ma nazwę hosta usługi App Service, zamiast oryginalna nazwa hosta "www.contoso.com".

## <a name="solution"></a>Rozwiązanie

Ten problem można rozwiązać przekierowanie nie ma potrzeby na stronie aplikacji, jeśli nie jest to możliwe, przekazujemy tego samego nagłówka hosta odbiera Application Gateway w usłudze App Service również zamiast wykonywania przesłonięcia hosta.

Po czynność tę możemy wykonać, usługi App Service wykona przekierowanie (jeśli istnieje) w tej samej oryginalnego nagłówka hosta, który wskazuje usługa Application Gateway i nie swój własny.

Aby to osiągnąć, należy posiadać domenę niestandardową i postępuj zgodnie z procesem wymienione poniżej.

- Zarejestruj domeny do listy niestandardowej domeny usługi App Service. W tym celu musisz mieć rekord CNAME w domenę niestandardową, która wskazuje nazwę FQDN usługi App Service. Aby uzyskać więcej informacji, zobacz [mapowanie istniejącej niestandardowej nazwy DNS w usłudze Azure App Service](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain).![ appservice-2](.\media\troubleshoot-app-service-redirection-app-service-url\appservice-2.png)

- Po zakończeniu tej operacji usługi App Service jest gotowy do akceptowania nazwy hosta "www.contoso.com". Teraz Zmień wpis CNAME w systemie DNS, aby wskazywać z powrotem nazwy FQDN bramy aplikacji. Na przykład "appgw.eastus.cloudapp.azure.com".

- Upewnij się, że domenę "www.contoso.com" jest rozpoznawany jako nazwa FQDN bramy aplikacji podczas wykonywania zapytania DNS.

- Ustaw swojej niestandardowej sondy, aby wyłączyć "Wybierz nazwę hosta z ustawienia HTTP zaplecza". To jest możliwe z poziomu portalu, usuwając zaznaczenie pola wyboru w obszarze Ustawienia sondowania i w programie PowerShell, nie korzystając - PickHostNameFromBackendHttpSettings przełącznika.

  > [!NOTE]
  > Podczas wykonywania tego kroku upewnij się, że swojej niestandardowej sondy nie jest skojarzona z ustawień HTTP zaplecza.

- Określ ustawienia HTTP usługi Application Gateway można wyłączyć "Wybierz nazwę hosta z wewnętrznej bazy danych Address". Możesz zrobić z poziomu portalu, usuwając zaznaczenie pola wyboru, a w programie PowerShell, nie korzystając - PickHostNameFromBackendAddress przełącznika.

- Sprawdź kondycję wewnętrznej bazy danych, jeśli jest w dobrej kondycji i skojarzyć niestandardowej sondy do ustawień HTTP zaplecza.

- Po zakończeniu tej operacji usługa Application Gateway obecnie może przekazywać tej samej nazwy hosta "www.contoso.com" w usłudze App Service, a następnie nastąpi przekierowanie na tej samej nazwy hosta. Można sprawdzić na przykład nagłówki żądania i odpowiedzi poniżej.

  Nagłówki żądania do usługi Application Gateway:

  Adres URL żądania: http://www.contoso.com/path

  Metoda żądania: GET

  Host: [www.contoso.com](http://www.contoso.com)

  Nagłówki odpowiedzi:

  Kod stanu: 301 trwale przeniesiona

  Lokalizacja: http://www.contoso.com/path/

  Serwer: Microsoft-IIS/10.0

  Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=www.contoso.com

  X obsługiwane przez: ASP.NET

## <a name="next-steps"></a>Kolejne kroki

Jeśli poprzednie kroki nie rozwiązują ten problem, otwórz [bilet pomocy technicznej](https://azure.microsoft.com/support/options/).