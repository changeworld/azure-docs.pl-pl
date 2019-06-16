---
title: Wdróż narzędzie do zarządzania — platformy Azure
description: Jak zainstalować narzędzia interfejsu użytkownika do zarządzania zasobami w wersji zapoznawczej Windows pulpitu wirtualnego.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: v-chjenk
ms.openlocfilehash: 3635cd422e4c7a064d3317401b734f3bc6ccb6c6
ms.sourcegitcommit: e5dcf12763af358f24e73b9f89ff4088ac63c6cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2019
ms.locfileid: "67136446"
---
# <a name="tutorial-deploy-a-management-tool"></a>Samouczek: Wdróż narzędzie do zarządzania

Narzędzie do zarządzania udostępnia interfejs użytkownika (UI) do zarządzania zasobami Microsoft wirtualnego pulpitu w wersji zapoznawczej. W tym samouczku dowiesz się, jak wdrożyć i nawiązać połączenie z narzędzia do zarządzania.

>[!NOTE]
>Te instrukcje dotyczą konfiguracji specyficznych dla Windows pulpitu wirtualnego (wersja zapoznawcza), który może być używany z istniejącymi procesami w Twojej organizacji.

## <a name="important-considerations"></a>Istotne zagadnienia

Ponieważ aplikacja wymaga zgody na interakcję z pulpitem wirtualnym Windows, to narzędzie nie obsługuje różnych scenariuszy biznesowych (B2B). Każda dzierżawa usługi Azure Active Directory (AAD) subskrypcji należy swój własny wdrażane pojedynczo, narzędzia do zarządzania.

To narzędzie do zarządzania znajduje się przykładowy. Firma Microsoft oferuje ważnych zabezpieczeń i aktualizacje dotyczące jakości. [Kod źródłowy jest dostępny w witrynie GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Klienci i partnerzy, zaleca się Dostosowywanie narzędzia do ich potrzeb biznesowych.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Co jest potrzebne uruchomić szablon usługi Azure Resource Manager

Przed przystąpieniem do wdrażania szablonu usługi Azure Resource Manager, musisz wdrożyć interfejs użytkownika zarządzania użytkownika usługi Azure Active Directory. Ten użytkownik musi:

- Wyłączono usługi Azure Multi-Factor Authentication (MFA)
- Ma uprawnienia do tworzenia zasobów w subskrypcji platformy Azure
- Ma uprawnienia do tworzenia aplikacji usługi Azure AD. Wykonaj następujące kroki, aby sprawdzić, czy użytkownik ma [wymagane uprawnienia](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

Po wdrożeniu szablonu usługi Azure Resource Manager, należy uruchomić Zarządzanie interfejsu użytkownika do sprawdzania poprawności. Ten użytkownik musi:
- Masz przypisania roli, aby wyświetlić lub edytować dzierżawy Windows pulpitu wirtualnego

## <a name="run-the-azure-resource-manager-template-to-provision-the-management-ui"></a>Uruchom szablon usługi Azure Resource Manager do aprowizacji interfejs użytkownika zarządzania

Przed rozpoczęciem upewnij się aplikacji serwera i klienta mają zgody, odwiedzając [Windows wirtualnego pulpitu zgody strony](https://rdweb.wvd.microsoft.com) dla usługi Azure Active Directory (AAD) reprezentowana.

Wykonaj te instrukcje, aby wdrożyć szablon usługi Azure Resource Management:

1. Przejdź do [strony usług pulpitu zdalnego — szablony platformy Azure GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy).
2. Wdróż szablon na platformie Azure.
    - Jeśli wdrażasz subskrypcji Enterprise, przewiń w dół i wybierz **Wdróż na platformie Azure**. Zobacz [wskazówki dotyczące parametrów szablonu](#guidance-for-template-parameters).
    - Jeśli jest wdrażany w ramach subskrypcji Cloud Solution Provider, wykonaj te instrukcje, aby wdrożyć na platformie Azure:
        1. Przewiń w dół i kliknij prawym przyciskiem myszy **Wdróż na platformie Azure**, a następnie wybierz **lokalizacja Linku kopiowania**.
        2. Otwórz Edytor tekstów, takiego jak Notatnik i Wklej link istnieje.
        3. Od razu po <https://portal.azure.com/> i przed hasztagiem (#), wprowadź znak (@) następuje nazwa domeny dzierżawy. Poniżej przedstawiono przykładowy format: <https://portal.azure.com/@Contoso.onmicrosoft.com#create/>.
        4. Zaloguj się do witryny Azure portal jako użytkownik z uprawnieniami administratora/współautora do subskrypcji dostawcy rozwiązań w chmurze.
        5. Wklej łącze, które zostały skopiowane do edytora tekstu na pasku adresu.

### <a name="guidance-for-template-parameters"></a>Wskazówki dotyczące parametrów szablonu
Poniżej przedstawiono sposób konfigurowania narzędzia wprowadź parametry:

- To jest adres URL brokera usług pulpitu zdalnego:  <https://rdbroker.wvd.microsoft.com/>
- Jest to adres URL zasobu:  <https://mrs-prod.ame.gbl/mrs-RDInfra-prod>
- Użyj swoich poświadczeń usługi AAD za pomocą usługi MFA, wyłączona, aby zalogować się do platformy Azure. Zobacz [co jest potrzebne uruchomić szablon usługi Azure Resource Manager](#what-you-need-to-run-the-azure-resource-manager-template).
- Użyj unikatowej nazwy dla aplikacji, który ma zostać zarejestrowany w usłudze Azure Active Directory dla narzędzia do zarządzania; na przykład Apr3UX.

## <a name="use-the-management-tool"></a>Użyj narzędzia do zarządzania

Po GitHub usługi Azure Resource Manager kończy szablonu, można znaleźć grupy zasobów, zawierający dwie usługi aplikacji wraz z jednego planu usługi app service w witrynie Azure portal.

Wykonaj te instrukcje, aby uruchomić narzędzie:

1. Wybierz zasób usługi Azure App Services przy użyciu podanej nazwy w szablonie (na przykład Apr3UX), a następnie przejdź do adresu URL skojarzone z nim; na przykład <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Zaloguj się przy użyciu poświadczeń Windows pulpitu wirtualnego.
3. Po wyświetleniu monitu, aby wybrać grupę dzierżawy, wybierz **domyślne grupy dzierżawy** z listy rozwijanej.

> [!NOTE]
> Jeśli masz grupę niestandardową dzierżawy, wprowadź nazwę ręcznie, zamiast wybierać z listy rozwijanej.

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz sposób wdrażania i nawiązać połączenie z narzędzia do zarządzania, możesz dowiedzieć się, jak monitorować usługi, problemów i porady dotyczące kondycji za pomocą usługi Azure Service Health.

> [!div class="nextstepaction"]
> [Konfigurowanie usługi alerty samouczek](./set-up-service-alerts.md)
