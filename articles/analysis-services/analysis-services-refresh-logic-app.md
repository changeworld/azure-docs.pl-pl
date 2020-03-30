---
title: Odświeżanie za pomocą aplikacji logiki dla modeli usług Azure Analysis Services | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób kodować odświeżanie asynchroniczne dla usług Azure Analysis Services przy użyciu aplikacji Azure Logic Apps.
author: chrislound
ms.service: analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: chlound
ms.openlocfilehash: 78bc629598c0635b7760285d0507b7a85a4ab551
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126858"
---
# <a name="refresh-with-logic-apps"></a>Odświeżanie za pomocą usługi Logic Apps

Za pomocą aplikacji logiki i wywołań REST, można wykonywać automatyczne operacje odświeżania danych na platformie Analizy modeli tabelarniczych, w tym synchronizacji replik tylko do odczytu dla kwerendy skalowalnej w poziomie.

Aby dowiedzieć się więcej na temat korzystania z interfejsów API REST za pomocą usług Azure Analysis Services, zobacz [Odświeżanie asynchroniczne za pomocą interfejsu API REST](analysis-services-async-refresh.md).

## <a name="authentication"></a>Uwierzytelnianie

Wszystkie wywołania muszą być uwierzytelnione przy użyciu prawidłowego tokenu usługi Azure Active Directory (OAuth 2).  Przykłady w tym artykule użyje jednostki usługi (SPN) do uwierzytelniania w usłudze Azure Analysis Services. Aby dowiedzieć się więcej, zobacz [Tworzenie jednostki usługi przy użyciu witryny Azure Portal.](../active-directory/develop/howto-create-service-principal-portal.md)

## <a name="design-the-logic-app"></a>Projektowanie aplikacji logiki

> [!IMPORTANT]
> W poniższych przykładach przyjęto założenie, że zapora usług Azure Analysis Services jest wyłączona. Jeśli zapora jest włączona, publiczny adres IP inicjatora żądania musi znajdować się na białej liście w zaporze usług Azure Analysis Services. Aby dowiedzieć się więcej o zakresach adresów IP usługi Azure Logic Apps na region, zobacz [Limity i informacje o konfiguracji aplikacji Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#configuration).

### <a name="prerequisites"></a>Wymagania wstępne

#### <a name="create-a-service-principal-spn"></a>Tworzenie jednostki usługi (SPN)

Aby dowiedzieć się więcej o tworzeniu jednostki usługi, zobacz [Tworzenie jednostki usługi przy użyciu witryny Azure portal.](../active-directory/develop/howto-create-service-principal-portal.md)

#### <a name="configure-permissions-in-azure-analysis-services"></a>Konfigurowanie uprawnień w usługach Azure Analysis Services
 
Utworzona jednostka usługi musi mieć uprawnienia administratora serwera na serwerze. Aby dowiedzieć się więcej, zobacz [Dodawanie jednostki usługi do roli administratora serwera](analysis-services-addservprinc-admins.md).

### <a name="configure-the-logic-app"></a>Konfigurowanie aplikacji logiki

W tym przykładzie aplikacja logiki jest przeznaczony do wyzwalania po odebraniu żądania HTTP. Umożliwi to użycie narzędzia aranżacji, takiego jak usługa Azure Data Factory, w celu wyzwolenia odświeżania modelu usług Azure Analysis Services.

Po utworzeniu aplikacji logiki:

1. W projektancie aplikacji logiki wybierz pierwszą akcję jako **Po odebraniu żądania HTTP**.

   ![Dodawanie działania odebranego http](./media/analysis-services-async-refresh-logic-app/1.png)

Ten krok zostanie wypełniony adresem URL HTTP POST po zapisaniu aplikacji logiki.

2. Dodaj nowy krok i wyszukaj **http**.  

   ![Dodawanie działania HTTP](./media/analysis-services-async-refresh-logic-app/9.png)

   ![Dodawanie działania HTTP](./media/analysis-services-async-refresh-logic-app/10.png)

3. Wybierz **http,** aby dodać tę akcję.

   ![Dodawanie działania HTTP](./media/analysis-services-async-refresh-logic-app/2.png)

Skonfiguruj działanie HTTP w następujący sposób:

|Właściwość  |Wartość  |
|---------|---------|
|**Metoda**     |POST         |
|**Identyfikator URI**     | https://*regionu serwera*/servers/*aas nazwa serwera*/models/ nazwa bazy*danych*/odświeża <br /> <br /> Na przykład: https:\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes|
|**Nagłówki**     |   Typ zawartości, aplikacja/json <br /> <br />  ![Nagłówki](./media/analysis-services-async-refresh-logic-app/6.png)    |
|**Treść**     |   Aby dowiedzieć się więcej na temat tworzenia treści żądania, zobacz [Odświeżanie asynchroniczne za pomocą interfejsu API REST — POST /refreshes](analysis-services-async-refresh.md#post-refreshes). |
|**Uwierzytelnianie**     |Active Directory OAuth         |
|**Dzierżawa**     |Wypełnij identyfikator dzierżawy usługi Azure Active Directory         |
|**Grupy odbiorców**     |https://*.asazure.windows.net         |
|**Client ID (Identyfikator klienta)**     |Wprowadź identyfikator klienta głównej nazwy usługi         |
|**Typ poświadczeń**     |Wpis tajny         |
|**Wpis tajny**     |Wprowadź klucz tajny nazwy głównej usługi         |

Przykład:

![Ukończone działanie HTTP](./media/analysis-services-async-refresh-logic-app/7.png)

Teraz przetestuj aplikację logiki.  W projektancie aplikacji logiki kliknij przycisk **Uruchom**.

![Testowanie aplikacji logiki](./media/analysis-services-async-refresh-logic-app/8.png)

## <a name="consume-the-logic-app-with-azure-data-factory"></a>Korzystanie z aplikacji logiki za pomocą usługi Azure Data Factory

Po zapisaniu aplikacji logiki przejrzyj działanie **Po odebraniu żądania HTTP,** a następnie skopiuj generowany adres **URL HTTP POST.**  Jest to adres URL, który może służyć przez usługę Azure Data Factory, aby wywołać asynchronii, aby wyzwolić aplikację logiki.

Oto przykład działania usługi Azure Data Factory Web, która wykonuje tę akcję.

![Aktywność w sieci Web fabryki danych](./media/analysis-services-async-refresh-logic-app/11.png)

## <a name="use-a-self-contained-logic-app"></a>Korzystanie z niezależnej aplikacji logiki

Jeśli nie planujesz używania narzędzia aranżacji, takiego jak Fabryka danych, aby wyzwolić odświeżanie modelu, możesz ustawić aplikację logiki, aby wyzwolić odświeżanie na podstawie harmonogramu.

Korzystając z powyższego przykładu, usuń pierwsze działanie i zastąp je działaniem **harmonogramu.**

![Zaplanuj aktywność](./media/analysis-services-async-refresh-logic-app/12.png)

![Zaplanuj aktywność](./media/analysis-services-async-refresh-logic-app/13.png)

W tym **przykładzie użyjemy cyklu**.

Po dodaniu działania skonfiguruj interwał i częstotliwość, a następnie dodaj nowy parametr i wybierz w **tych godzinach**.

![Zaplanuj aktywność](./media/analysis-services-async-refresh-logic-app/16.png)

Wybierz poszukiwane godziny.

![Zaplanuj aktywność](./media/analysis-services-async-refresh-logic-app/15.png)

Zapisz aplikację logiki.

## <a name="next-steps"></a>Następne kroki

[Próbki](analysis-services-samples.md)  
[INTERFEJS API ODPOCZYNKU](https://docs.microsoft.com/rest/api/analysisservices/servers)
