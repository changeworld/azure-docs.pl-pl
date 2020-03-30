---
title: Instalowanie lokalnej bramy danych dla usług Azure Analysis Services | Dokumenty firmy Microsoft
description: Dowiedz się, jak zainstalować i skonfigurować lokalną bramę danych do łączenia się z lokalnymi źródłami danych z serwera usług Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f6218b32fb9574adf62384d2a6ee5a62f3788de8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062153"
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Instalowanie i konfigurowanie bramy danych lokalnych

Lokalna brama danych jest wymagana, gdy jeden lub więcej serwerów usług Azure Analysis Services w tym samym regionie łączy się z lokalnymi źródłami danych.  Podczas gdy instalowana brama jest taka sama, jak używana przez inne usługi, takie jak Usługa Power BI, Power Apps i Aplikacje logiki, podczas instalowania usług Azure Analysis Services, należy wykonać kilka dodatkowych kroków. Ten artykuł dotyczący instalacji jest specyficzny dla **usług Azure Analysis Services**. 

Aby dowiedzieć się więcej o tym, jak usługi Azure Analysis Services współpracuje z bramą, zobacz [Łączenie się z lokalnymi źródłami danych.](analysis-services-gateway.md) Aby dowiedzieć się więcej o zaawansowanych scenariuszach instalacji i ogólnie o bramie, zobacz [Dokumentacja lokalnych bram danych](/data-integration/gateway/service-gateway-onprem).

## <a name="prerequisites"></a>Wymagania wstępne

**Minimalne wymagania:**

* Program .NET Framework 4.5
* 64-bitowa wersja systemu Windows 8 / Windows Server 2012 R2 (lub nowsza)

**Zalecane:**

* Procesor 8-rdzeniowy
* 8 GB pamięci
* 64-bitowa wersja systemu Windows 8 / Windows Server 2012 R2 (lub nowsza)

**Ważne kwestie:**

* Podczas konfigurowania podczas rejestrowania bramy na platformie Azure jest wybierany domyślny region subskrypcji. Możesz wybrać inną subskrypcję i region. Jeśli masz serwery w więcej niż jednym regionie, musisz zainstalować bramę dla każdego regionu. 
* Nie można zainstalować bramy na kontrolerze domeny.
* Na jednym komputerze można zainstalować tylko jedną bramę.
* Zainstaluj bramę na komputerze, który pozostaje włączony i nie przechodzi w tryb uśpienia.
* Nie należy instalować bramy na komputerze z bezprzewodowym połączeniem tylko z siecią. Wydajność może być zmniejszona.
* Podczas instalowania bramy konto użytkownika, na którym zalogowano się do komputera, musi mieć uprawnienia logowania jako usługi. Po zakończeniu instalacji lokalna usługa bramy danych używa konta NT SERVICE\PBIEgwService do logowania się jako usługa. Podczas instalacji lub w usługach po zakończeniu instalacji można określić inne konto. Upewnij się, że ustawienia zasad grupy zezwalają zarówno na konto, na które zalogowano się podczas instalacji, jak i na konto usługi, na które wybierzesz pozycję Zaloguj się jako uprawnienia usługi.
* Zaloguj się na platformę Azure przy za pomocą konta w usłudze Azure AD dla tej samej [dzierżawy,](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) co subskrypcja, w której rejestrujesz bramę. Konta usługi Azure B2B (gość) nie są obsługiwane podczas instalowania i rejestrowania bramy.
* Jeśli źródła danych znajdują się w sieci wirtualnej platformy Azure (VNet), należy skonfigurować właściwość serwera [AlwaysUseGateway.](analysis-services-vnet-gateway.md)

## <a name="download"></a><a name="download"></a>Pobierz

 [Pobierz bramę](https://go.microsoft.com/fwlink/?LinkId=820925&clcid=0x409)

## <a name="install"></a><a name="install"></a>Zainstaluj

1. Uruchom konfigurację.

2. Wybierz **lokalną bramę danych**.

   ![Wybierz pozycję](media/analysis-services-gateway-install/aas-gateway-installer-select.png)

2. Wybierz lokalizację, zaakceptuj warunki, a następnie kliknij przycisk **Zainstaluj**.

   ![Instalowanie postanowień dotyczących lokalizacji i licencji](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Zaloguj się do platformy Azure. Konto musi znajdować się w usłudze Azure Active Directory dzierżawy. To konto jest używane dla administratora bramy. Konta usługi Azure B2B (gość) nie są obsługiwane podczas instalowania i rejestrowania bramy.

   ![Logowanie do platformy Azure](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Jeśli zalogujesz się przy za pomocą konta domeny, zostanie ono zamapowane na konto instytucji w usłudze Azure AD. Konto organizacji jest używane jako administrator bramy.

## <a name="register"></a><a name="register"></a>Zarejestruj

Aby utworzyć zasób bramy na platformie Azure, należy zarejestrować wystąpienie lokalne zainstalowane w usłudze gateway cloud service. 

1.  Wybierz **pozycję Zarejestruj nową bramę na tym komputerze**.

    ![Zarejestruj](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Wpisz nazwę i klucz odzyskiwania bramy. Domyślnie brama używa domyślnego regionu subskrypcji. Jeśli chcesz wybrać inny region, wybierz pozycję **Zmień region**.

    > [!IMPORTANT]
    > Zapisz klucz odzyskiwania w bezpiecznym miejscu. Klucz odzyskiwania jest wymagany do przejęcia, migracji lub przywrócenia bramy. 

   ![Zarejestruj](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-an-azure-gateway-resource"></a><a name="create-resource"></a>Tworzenie zasobu bramy platformy Azure

Po zainstalowaniu i zarejestrowaniu bramy należy utworzyć zasób bramy na platformie Azure. Zaloguj się na platformie Azure przy użyciu tego samego konta, które było używane podczas rejestrowania bramy.

1. W witrynie Azure portal kliknij pozycję **Utwórz zasób,** a następnie wyszukaj **lokalną bramę danych,** a następnie kliknij pozycję **Utwórz**.

   ![Tworzenie zasobu bramy](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. W **obszarze Tworzenie bramy połączenia**wprowadź następujące ustawienia:

   * **Nazwa**: Wprowadź nazwę zasobu bramy. 

   * **Subskrypcja**: Wybierz subskrypcję platformy Azure, aby skojarzyć z zasobem bramy. 
   
     Domyślna subskrypcja jest oparta na koncie platformy Azure, które zostało użyte do zalogowania się.

   * **Grupa zasobów**: utwórz grupę zasobów lub wybierz istniejącą.

   * **Lokalizacja:** Wybierz region, w którym została zarejestrowana brama.

   * **Nazwa instalacji:** Jeśli instalacja bramy nie jest jeszcze zaznaczona, wybierz bramę zainstalowaną na komputerze i zarejestrowaną. 

     Po zakończeniu kliknij przycisk **Utwórz**.

## <a name="connect-servers-to-the-gateway-resource"></a><a name="connect-servers"></a>Łączenie serwerów z zasobem bramy

1. W omówienie serwera usług Azure Analysis Services kliknij pozycję **Lokalna brama danych**.

   ![Łączenie serwera z bramą](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. W **obszarze Wybieranie lokalnej bramy danych do nawiązania połączenia**wybierz zasób bramy, a następnie kliknij pozycję **Połącz wybraną bramę**.

   ![Łączenie serwera z zasobem bramy](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Jeśli brama nie jest wyświetlana na liście, serwer prawdopodobnie nie znajduje się w tym samym regionie co region określony podczas rejestrowania bramy.

    Gdy połączenie między serwerem a zasobem bramy zakończy się pomyślnie, stan wyświetli **komunikat Połączony**.


    ![Łączenie serwera z sukcesem zasobów bramy](media/analysis-services-gateway-install/aas-gateway-connect-success.png)

Gotowe. Jeśli chcesz otworzyć porty lub wykonać jakiekolwiek rozwiązywanie problemów, zapoznaj się z [lokalną bramą danych](analysis-services-gateway.md).

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie usługami Analysis Services](analysis-services-manage.md)   
* [Pobierz dane z usług Azure Analysis Services](analysis-services-connect.md)   
* [Używanie bramy dla źródeł danych w usłudze Azure Virtual Network](analysis-services-vnet-gateway.md)
