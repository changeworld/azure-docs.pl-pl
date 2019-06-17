---
title: Odśwież za pomocą aplikacji logiki dla modeli usług Azure Analysis Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak napisać Odświeżanie asynchroniczne za pomocą usługi Azure Logic Apps.
author: chrislound
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: chlound
ms.openlocfilehash: 6ffce339fe7b1a434c8f007b417ee81a42529dfc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66142460"
---
# <a name="refresh-with-logic-apps"></a>Odświeżanie za pomocą usługi Logic Apps

Za pomocą aplikacji logiki i wywołania REST, można wykonać operacji odświeżania danych automatycznych w modeli tabelarycznych analiz platformy Azure, w tym synchronizacji repliki tylko do odczytu dla zapytania skalowalnego w poziomie.

Aby dowiedzieć się więcej na temat używania interfejsów API REST przy użyciu usług Azure Analysis Services, zobacz [asynchroniczne odświeżanie za pomocą interfejsu API REST](analysis-services-async-refresh.md).

## <a name="authentication"></a>Authentication

Wszystkie wywołania musi zostać uwierzytelniony przy użyciu prawidłowego tokenu usługi Azure Active Directory (OAuth 2).  Przykłady w niniejszym artykule użyje nazwy głównej usługi (SPN) do uwierzytelniania w usługach Azure Analysis Services. Aby dowiedzieć się więcej, zobacz [utworzyć nazwę główną usługi za pomocą witryny Azure portal](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="design-the-logic-app"></a>Projektowanie aplikacji logiki

> [!IMPORTANT]
> W poniższych przykładach założono, że Zapora usług Azure Analysis Services jest wyłączona.  Jeśli Zapora jest włączona, publiczny adres IP inicjatora żądanie musi być umieszczona na białej liście w zaporze usług Azure Analysis Services. Aby dowiedzieć się więcej na temat zakresów adresów IP aplikacji logiki na region, zobacz [limity i informacje o konfiguracji dla usługi Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses).

### <a name="prerequisites"></a>Wymagania wstępne

#### <a name="create-a-service-principal-spn"></a>Tworzenie jednostki usługi (SPN)

Aby dowiedzieć się więcej na temat tworzenia jednostki usługi, zobacz [utworzyć nazwę główną usługi za pomocą witryny Azure portal](../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="configure-permissions-in-azure-analysis-services"></a>Skonfiguruj uprawnienia w usługach Azure Analysis Services
 
Możesz utworzyć jednostkę usługi, musi mieć uprawnienia administratora serwera, na serwerze. Aby dowiedzieć się więcej, zobacz [Dodawanie jednostki usługi do roli administratora serwera](analysis-services-addservprinc-admins.md).

### <a name="configure-the-logic-app"></a>Konfigurowanie aplikacji logiki

W tym przykładzie aplikacja logiki została zaprojektowana do wyzwolenia po odebraniu żądania HTTP. Umożliwi to użycie narzędzia do aranżacji, takich jak Azure Data Factory, aby wyzwolić odświeżanie modelu usług Azure Analysis Services.

Po utworzeniu aplikacji logiki:

1. W Projektancie aplikacji logiki wybierz pierwszą akcją jako **zostanie odebrane żądanie po HTTP**.

   ![Dodaj działanie HTTP odebrane](./media/analysis-services-async-refresh-logic-app/1.png)

Ten krok spowoduje wypełnienie za pomocą adresu URL POST protokołu HTTP, po zapisaniu aplikacji logiki.

2. Dodaj nowy krok, a następnie wyszukaj **HTTP**.  

   ![Dodaj działanie HTTP](./media/analysis-services-async-refresh-logic-app/9.png)

   ![Dodaj działanie HTTP](./media/analysis-services-async-refresh-logic-app/10.png)

3. Wybierz **HTTP** można dodać tej akcji.

   ![Dodaj działanie HTTP](./media/analysis-services-async-refresh-logic-app/2.png)

Skonfiguruj aktywności protokołu HTTP w następujący sposób:

|Właściwość  |Wartość  |
|---------|---------|
|**— Metoda**     |POST         |
|**IDENTYFIKATOR URI**     | https://*regionu serwera*/servers/*jako nazwy serwera*/models/*Nazwa bazy danych*/ <br /> <br /> Na przykład: https:\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/|
|**Nagłówki**     |   Content-Type, application/json <br /> <br />  ![Nagłówki](./media/analysis-services-async-refresh-logic-app/6.png)    |
|**Treść**     |   Aby dowiedzieć się więcej na temat stanowiący treści żądania, zobacz [asynchroniczne odświeżanie za pomocą interfejsu API REST - POST /refreshes](analysis-services-async-refresh.md#post-refreshes). |
|**Uwierzytelnianie**     |OAuth usługi Active Directory         |
|**dzierżawy**     |Wprowadź identyfikator dzierżawy usługi Azure Active Directory         |
|**Grupy odbiorców**     |https://*.asazure.windows.net         |
|**Identyfikator klienta**     |Wprowadź swoje ClientID nazwę jednostki usługi         |
|**Typ poświadczeń**     |Secret         |
|**Wpis tajny**     |Wprowadź klucz tajny nazwę główną usługi         |

Przykład:

![Ukończone aktywności protokołu HTTP](./media/analysis-services-async-refresh-logic-app/7.png)

Teraz przetestuj aplikację logiki.  W Projektancie aplikacji logiki kliknij **Uruchom**.

![Testowanie aplikacji logiki](./media/analysis-services-async-refresh-logic-app/8.png)

## <a name="consume-the-logic-app-with-azure-data-factory"></a>Korzystanie z aplikacji logiki z usługą Azure Data Factory

Po zapisaniu aplikacji logiki, przejrzyj **zostanie odebrane żądanie po HTTP** działania, a następnie skopiuj **HTTP POST URL** teraz generowany.  To jest adres URL, który może służyć przez usługę Azure Data Factory do wykonania wywołania asynchronicznego wyzwalacza aplikacji logiki.

Oto przykład działania internetowego fabryki danych Azure, który wykonuje tę akcję.

![Działanie internetowe fabryki danych](./media/analysis-services-async-refresh-logic-app/11.png)

## <a name="use-a-self-contained-logic-app"></a>Użyj niezależna aplikacji logiki

Jeśli nie planujesz przy użyciu narzędzia do aranżacji takie jak Data Factory do wyzwalania odświeżania modelu, można ustawić aplikację logiki, aby wyzwolić odświeżanie, zgodnie z harmonogramem.

Korzystając z powyższego przykładu, usuń pierwsze działanie i zastąp go wartością **harmonogram** działania.

![Harmonogram działania](./media/analysis-services-async-refresh-logic-app/12.png)

![Harmonogram działania](./media/analysis-services-async-refresh-logic-app/13.png)

W tym przykładzie będzie używać **cyklu**.

Po dodaniu działania skonfigurować interwał i częstotliwość, a następnie dodano nowy parametr i wybierz **w tych godzinach**.

![Harmonogram działania](./media/analysis-services-async-refresh-logic-app/16.png)

Wybieranie odpowiedniej liczby godzin.

![Harmonogram działania](./media/analysis-services-async-refresh-logic-app/15.png)

Zapisz aplikację logiki.

## <a name="next-steps"></a>Kolejne kroki

[Przykłady](analysis-services-samples.md)  
[Interfejs API REST](https://docs.microsoft.com/rest/api/analysisservices/servers)
