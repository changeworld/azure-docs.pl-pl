---
title: Instalowanie bramy danych lokalnych | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zainstalować i skonfigurować bramę danych lokalnego.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9681d4895de4b5c5c5488fffa85a3314532b41a3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34598195"
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Zainstaluj i skonfiguruj bramę danych lokalnych
Bramę danych lokalnych jest wymagany, gdy łączą się lokalnych źródeł danych z co najmniej jeden serwer usług Azure Analysis Services, w tym samym regionie. Aby dowiedzieć się więcej o bramie, zobacz [bramy danych lokalnych](analysis-services-gateway.md).

## <a name="prerequisites"></a>Wymagania wstępne
**Minimalne wymagania:**

* .NET 4.5 framework
* 64-bitowej wersji systemu Windows 7 / Windows Server 2008 R2 (lub nowszy)

**Zalecane:**

* 8 rdzeni procesora CPU
* 8 GB pamięci
* 64-bitowej wersji systemu Windows 2012 R2 (lub nowszy)

**Ważne kwestie:**

* Podczas instalacji przy rejestracji bramy w usłudze Azure wybrano domyślnego regionu dla Twojej subskrypcji. Można wybrać inny region. Jeśli masz serwery w więcej niż jeden region, należy zainstalować bramy dla każdego regionu. 
* Brama nie można zainstalować na kontrolerze domeny.
* Na jednym komputerze można zainstalować tylko jedną bramę.
* Zainstalować bramę na komputerze, który pozostaje na, a nie przechodzi w stan uśpienia.
* Nie należy instalować bramy na komputerze bezprzewodowo podłączony do sieci. Wydajność może być mniejsza.
* Logowanie do platformy Azure przy użyciu konta w usłudze Azure AD dla tego samego [dzierżawy](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) jako subskrypcji w przypadku rejestracji bramy w. Funkcje B2B platformy Azure (Gość) konta nie są obsługiwane, gdy instalowanie i rejestrowanie bramy.
* (Unified) bramy, opisanych w tym miejscu nie jest obsługiwana w regionach suwerennych Azure dla instytucji rządowych, platformy Azure w Niemczech i Chińskiej wersji platformy Azure. Użyj **brama lokalna dedykowane dla usług Azure Analysis Services**zainstalowany na serwerze server **Szybki Start** w portalu. 


## <a name="download"></a>Pobierz
 [Pobierz bramę](https://aka.ms/azureasgateway)

## <a name="install"></a>Zainstaluj

1. Uruchom Instalatora.

2. Wybierz lokalizację, zaakceptuj warunki, a następnie kliknij **zainstalować**.

   ![Zainstaluj lokalizacji oraz postanowienia licencyjne](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Zaloguj się do platformy Azure. Konto musi znajdować się w Twojej dzierżawy usługi Azure Active Directory. To konto jest używane dla administratora bramy. Funkcje B2B platformy Azure (Gość) konta nie są obsługiwane, gdy instalowanie i rejestrowanie bramy.

   ![Logowanie do platformy Azure](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Jeśli możesz zalogować się przy użyciu konta domeny, jest mapowany do konta organizacyjnego w usłudze Azure AD. Konta organizacyjnego służy jako administratora bramy.

## <a name="register"></a>Rejestr
Aby utworzyć zasób bramy na platformie Azure, możesz zarejestrować lokalne wystąpienie instalowania z usługi bramy w chmurze. 

1.  Wybierz **zarejestrować nową bramę na tym komputerze**.

    ![Zarejestruj subskrypcję](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Wpisz nazwę i odzyskiwanie klucza bramy. Domyślnie przy użyciu brama swoją subskrypcję domyślnego regionu. Jeśli chcesz wybrać inny region, wybierz **Region zmiany**.

    > [!IMPORTANT]
    > Klucz odzyskiwania należy zapisać w bezpiecznym miejscu. Klucz odzyskiwania jest wymagany w kolejności do przejęcia, migracji lub przywracania bramy. 

   ![Zarejestruj subskrypcję](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-resource"></a>Utwórz zasób Azure bramy
Po zainstalowaniu i zarejestrować bramę, należy utworzyć zasób bramy w Twojej subskrypcji platformy Azure. Zaloguj się do platformy Azure z tego samego konta używanego podczas rejestrowania bramy.

1. W portalu Azure kliknij **Utwórz nową usługę** > **integracji przedsiębiorstwa** > **bramy danych lokalnych** > **Utwórz**.

   ![Utwórz zasób bramy](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. W **Utwórz bramę połączenia**, wprowadź następujące ustawienia:

    * **Nazwa**: Wprowadź nazwę dla zasobu bramy. 

    * **Subskrypcja**: Wybierz subskrypcję platformy Azure do skojarzenia z zasobu bramy. 
   
      Domyślna subskrypcja opiera się na konto platformy Azure, używany do logowania.

    * **Grupa zasobów**: utwórz grupę zasobów lub wybierz istniejącą.

    * **Lokalizacja**: Wybierz region, w zarejestrowany bramy w.

    * **Nazwa instalacji**: Jeśli instalacji bramy nie została jeszcze wybrana, wybierz bramę, w zarejestrowany. 

    Gdy wszystko będzie gotowe, kliknij przycisk **Utwórz**.

## <a name="connect-servers"></a>Połącz serwery do zasobu bramy

1. W sieci — Omówienie serwera usług Azure Analysis Services kliknij **bramy danych lokalnych**.

   ![Połącz serwer bramy](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. W **wybierz bramę danych lokalnego, aby połączyć**wybierz zasób bramy, a następnie kliknij przycisk **wybranej bramy Połącz**.

   ![Połącz serwer bramy zasobów](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Jeśli na liście nie ma bramy, serwer, prawdopodobnie nie w tym samym regionie co region określone podczas rejestrowania bramy. 

To już wszystko. Jeśli trzeba otworzyć porty lub czy rozwiązywania wszelkich problemów, koniecznie zapoznaj się z [bramy danych lokalnych](analysis-services-gateway.md).

## <a name="next-steps"></a>Kolejne kroki
* [Zarządzanie usług Analysis Services](analysis-services-manage.md)   
* [Pobieranie danych z usług Azure Analysis Services](analysis-services-connect.md)
