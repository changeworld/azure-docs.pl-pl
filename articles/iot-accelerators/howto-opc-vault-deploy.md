---
title: Jak wdrożyć usługę zarządzania certyfikatami magazynu OPC — Azure | Microsoft Docs
description: Jak wdrożyć usługę zarządzania certyfikatami magazynu OPC od podstaw.
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 1625f0e6f9bfe8297cae2770e63107bf4f19f95e
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012986"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>Kompilowanie i wdrażanie usługi zarządzania certyfikatami magazynu OPC

W tym artykule opisano sposób wdrażania usługi zarządzania certyfikatami magazynu OPC na platformie Azure.

> [!NOTE]
> Aby uzyskać więcej informacji na temat szczegółów wdrożenia i instrukcje, zobacz [repozytorium magazynu OPC](https://github.com/Azure/azure-iiot-opc-vault-service)usługi GitHub.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="install-required-software"></a>Zainstaluj wymagane oprogramowanie

Obecnie operacja kompilowania i wdrażania jest ograniczona do systemu Windows.
Przykłady są przeznaczone dla C# programu .NET Standard, co jest konieczne do utworzenia usługi i przykładów do wdrożenia.
Wszystkie narzędzia potrzebne do programu .NET standard są dostarczane z narzędziami .Net Core. Zobacz [tutaj](https://docs.microsoft.com/dotnet/articles/core/getting-started) , czego potrzebujesz.

1. [Zainstaluj program .NET Core 2.1 +][dotnet-install].
2. [Zainstaluj platformę Docker][docker-url] (opcjonalnie tylko wtedy, gdy wymagana jest lokalna kompilacja platformy Docker).
4. Zainstaluj [narzędzia wiersza polecenia platformy Azure dla programu PowerShell][powershell-install].
5. Zarejestruj się, aby uzyskać [subskrypcję platformy Azure][azure-free].

### <a name="clone-the-repository"></a>Klonowanie repozytorium

Jeśli jeszcze tego nie zrobiono, Sklonuj to repozytorium GitHub.  Otwórz wiersz polecenia lub terminal i uruchom polecenie:

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

lub Sklonuj repozytorium bezpośrednio w programie Visual Studio 2017.

### <a name="build-and-deploy-the-azure-service-on-windows"></a>Kompilowanie i wdrażanie usługi platformy Azure w systemie Windows

Skrypt programu PowerShell zapewnia łatwy sposób wdrażania mikrousług magazynu OPC i aplikacji.<br>

1. Otwórz okno programu PowerShell w katalogu głównym repozytorium. 
3. Przejdź do folderu Wdróż`cd deploy`
3. Wybierz nazwę `myResourceGroup` , która prawdopodobnie nie spowoduje konfliktu z innymi wdrożonymi stronami sieci Web. Zobacz [poniżej](#website-name-already-in-use) , jak nazwy stron sieci Web są wybierane na podstawie nazwy grupy zasobów.
5. Rozpocznij wdrażanie za pomocą `.\deploy.ps1` programu dla instalacji interaktywnej<br>
lub wprowadź pełny wiersz polecenia:  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. Jeśli planujesz Programowanie przy użyciu tego wdrożenia, Dodaj `-development 1` , aby włączyć interfejs użytkownika programu Swagger i wdrożyć kompilacje debugowania.
6. Postępuj zgodnie z instrukcjami w skrypcie, aby zalogować się do subskrypcji i podać dodatkowe informacje.
9. Po pomyślnym zakończeniu kompilacji i wdrożenia powinien zostać wyświetlony następujący komunikat:
   ```
   To access the web client go to:
   https://myResourceGroup.azurewebsites.net

   To access the web service go to:
   https://myResourceGroup-service.azurewebsites.net

   To start the local docker GDS server:
   .\myResourceGroup-dockergds.cmd

   To start the local dotnet GDS server:
   .\myResourceGroup-gds.cmd
   ```

W przypadku problemów z działaniem wykonaj poniższe kroki [](#troubleshooting-deployment-failures).

8. Otwórz ulubioną przeglądarkę i Otwórz stronę aplikacji:`https://myResourceGroup.azurewebsites.net`
8. Nadajesz aplikacji sieci Web i mikrousługom magazynu OPC kilka minut, aby rozgrzać po wdrożeniu. Strona główna sieci Web może zawiesić się przy pierwszym użyciu przez maksymalnie minutę do momentu otrzymania pierwszych odpowiedzi.
11. Aby zapoznać się z otwartym interfejsem API programu Swagger:`https://myResourceGroup-service.azurewebsites.net`
13. Aby uruchomić lokalny serwer GDS z uruchamianiem `.\myResourceGroup-gds.cmd` dotnet lub przy użyciu platformy Docker, Uruchom. `.\myResourceGroup-dockergds.cmd`

W sidenote można ponownie wdrożyć kompilację z dokładnie tymi samymi ustawieniami. Należy pamiętać, że taka operacja odnawia wszystkie wpisy tajne aplikacji i może zresetować niektóre ustawienia w rejestracjach aplikacji Azure Active Directory (Azure AD).

Możliwe jest również ponowne wdrożenie tylko plików binarnych aplikacji sieci Web. Z parametrami `-onlyBuild 1` nowe pakiety zip usługi i aplikacja jest wdrażana w aplikacjach sieci Web.

Po pomyślnym wdrożeniu możesz zacząć korzystać z usług: [Jak zarządzać usługą zarządzania certyfikatami magazynu OPC](howto-opc-vault-manage.md)

## <a name="delete-the-services-from-the-subscription"></a>Usuwanie usług z subskrypcji

1. Zaloguj się do Azure Portal: `https://portal.azure.com`.
2. Przejdź do grupy zasobów, w której została wdrożona usługa.
3. Wybierz `Delete resource group` i potwierdź.
4. Po krótkim czasie wszystkie wdrożone składniki usługi zostaną usunięte.
5. Teraz przejdź do `Azure Active Directory/App registrations`.
6. Dla każdej wdrożonej grupy zasobów powinny być wymienione trzy rejestracje o następujących nazwach: `resourcegroup-client`, `resourcegroup-module`, `resourcegroup-service`.
Każdą rejestrację należy usunąć osobno.
7. Teraz wszystkie wdrożone składniki są usuwane.

## <a name="troubleshooting-deployment-failures"></a>Rozwiązywanie problemów z błędami wdrożenia

### <a name="resource-group-name"></a>Nazwa grupy zasobów

Upewnij się, że używana jest krótka i prosta Nazwa grupy zasobów.  Nazwa służy również do nazywania zasobów i prefiksu adresu URL usługi, a w związku z tym musi być zgodna z wymaganiami dotyczącymi nazw zasobów.  

### <a name="website-name-already-in-use"></a>Nazwa witryny sieci Web jest już używana

Istnieje możliwość, że nazwa witryny sieci Web jest już używana.  W przypadku uruchomienia tego błędu należy użyć innej nazwy grupy zasobów. Nazwy hostów używane przez skrypt wdrażania są następujące: https://resourcegroupname.azurewebsites.net i. https://resourgroupname-service.azurewebsites.net
Inne nazwy usług są tworzone przez kombinację skrótów krótkich nazw i prawdopodobnie nie powodują konfliktu z innymi usługami.

### <a name="azure-active-directory-azure-ad-registration"></a>Rejestracja w usłudze Azure Active Directory (Azure AD) 

Skrypt wdrażania próbuje zarejestrować trzy aplikacje usługi Azure AD w Azure Active Directory.  
W zależności od uprawnień w wybranej dzierżawie usługi Azure AD ta operacja może zakończyć się niepowodzeniem.   Dostępne są dwie opcje:

1. W przypadku wybrania dzierżawy usługi Azure AD z listy dzierżawców Uruchom ponownie skrypt i wybierz inny z listy.
2. Alternatywnie możesz wdrożyć prywatną dzierżawę usługi Azure AD w innej subskrypcji, uruchomić ponownie skrypt i wybrać go do użycia.

## <a name="deployment-script-options"></a>Opcje skryptu wdrożenia

Skrypt przyjmuje następujące parametry:


```
-resourceGroupName
```

Może to być nazwa istniejącej lub nowej grupy zasobów.

```
-subscriptionId
```


Opcjonalne, Identyfikator subskrypcji, w której zostaną wdrożone zasoby.

```
-subscriptionName
```


Lub alternatywnie nazwę subskrypcji.

```
-resourceGroupLocation
```


Opcjonalna — lokalizacja grupy zasobów. Jeśli ta wartość jest określona, program podejmie próbę utworzenia nowej grupy zasobów w tej lokalizacji.


```
-tenantId
```


Dzierżawa usługi Azure AD do użycia. 

```
-development 0|1
```

Opcjonalne, aby wdrożyć na potrzeby programowania. Użyj kompilacji debugowania i ustaw środowisko ASP.Net na programowanie. Utwórz element ". publishsettings" do zaimportowania w programie Visual Studio 2017, aby umożliwić mu bezpośrednie wdrożenie aplikacji i usługi.

```
-onlyBuild 0|1
```

Opcjonalnie, aby ponownie skompilować i wdrożyć tylko aplikacje sieci Web i ponownie skompilować kontenery platformy Docker.

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://www.microsoft.com/net/learn/get-started

## <a name="next-steps"></a>Następne kroki

Teraz, kiedy wiesz już, jak wdrożyć magazyn OPC od podstaw, poniżej przedstawiono sugerowany następny krok:

> [!div class="nextstepaction"]
> [Zarządzanie magazynem OPC](howto-opc-vault-manage.md)
