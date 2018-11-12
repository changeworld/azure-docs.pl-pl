---
title: Zrozumienie kontroli dostępu opartej na rolach Twins cyfrowych platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, uwierzytelnianie w reprezentacji urządzeń cyfrowych przy użyciu kontroli dostępu opartej na rolach.
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: lyrana
ms.openlocfilehash: b9ccdb9030a24520be8f24f757c279241f3a07e1
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2018
ms.locfileid: "51014792"
---
# <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Twins cyfrowych platformy Azure umożliwia kontrolowanie precyzyjnego dostępu do określonych danych, zasobów i akcji w grafie przestrzennych. Robi to za pośrednictwem szczegółowe zarządzanie rolami i uprawnieniami o nazwie kontroli dostępu opartej na rolach (RBAC). RBAC składa się z _role_ i _przypisań ról_. Role Określ poziom uprawnień. Przypisania ról skojarzyć roli z użytkowników lub urządzeń.

Korzystając z modelu RBAC uprawnienia nadane:

- Użytkownik.
- Urządzenie.
- Jednostki usługi.
- Funkcja zdefiniowana przez użytkownika. 
- Wszyscy użytkownicy, którzy należą do domeny. 
- Dzierżawca.
 
Poziom dostępu, również można dostosować.

RBAC jest unikatowa, w tym, że uprawnienia są dziedziczone w dół przestrzenne programu graph.

## <a name="what-can-i-do-with-rbac"></a>Co można zrobić za pomocą kontroli dostępu opartej na rolach?

Deweloper może używać funkcji RBAC w usłudze:

* Udzielić użytkownikowi możliwość zarządzania urządzeniami dla całego kompilowania lub tylko dla konkretnego pomieszczenia lub piętra.
* Udziel administrator globalny dostęp do wszystkich węzłów przestrzenne wykresu cały wykres, czy tylko w części wykresu.
* Udzielanie dostępu Odczyt specjalista ds. pomocy technicznej do grafu, z wyjątkiem klucze dostępu.
* Przyznaj każdy członek domeny do odczytu wszystkich obiektów grafu.

## <a name="rbac-best-practices"></a>Najlepsze rozwiązania RBAC

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>Role

### <a name="role-definitions"></a>Definicje ról

Definicja roli to zbiór uprawnień i jest czasami nazywane roli. Listy definicji roli, z którymi dozwolone operacje, które obejmują tworzenia, odczytu, aktualizacji i usuwania. Określa również, typy obiektów, których dotyczą te uprawnienia.

Następujące role są dostępne w reprezentacji urządzeń cyfrowych platformy Azure:

* **Administrator miejsca**: tworzenia, odczytu, aktualizacji i usuwania uprawnień do określonego miejsca i na wszystkich węzłach poniżej. Uprawnienie globalne.
* **Administrator użytkowników**: tworzenia, odczytu, aktualizacji i usuwania uprawnień dla użytkowników i obiektów związanych z użytkownika. Uprawnienia do odczytu dla miejsca do magazynowania.
* **Rola Administrator urządzenia**: tworzenia, odczytu, aktualizacji i usuwania uprawnień dla urządzeń i obiektów związanych z urządzenia. Uprawnienia do odczytu dla miejsca do magazynowania.
* **Klucz administratora**: tworzenia, odczytu, aktualizacji i usuwania uprawnień dostępu do kluczy. Uprawnienia do odczytu dla miejsca do magazynowania.
* **Token administratora**: uprawnienia odczytu i aktualizacji dla kluczy dostępu. Uprawnienia do odczytu dla miejsca do magazynowania.
* **Użytkownik**: uprawnienia do odczytu miejsca do magazynowania, czujniki i użytkowników, która obejmuje odpowiadające im obiektów związanych z.
* **Specjalista ds. obsługi**: uprawnienia do wszystkim, z wyjątkiem kluczy dostępu do odczytu.
* **Instalator urządzenia**: uprawnienie odczytu i aktualizacji dla urządzeń i czujników, które zawiera odpowiednie powiązane obiekty. Uprawnienia do odczytu dla miejsca do magazynowania.
* **Urządzenie bramy**: Utwórz uprawnienie do czujników. Uprawnienia do odczytu urządzeń i czujników, w tym odpowiadające im powiązanych obiektów.

>[!NOTE]
> Aby pobrać pełną definicje ról poprzedniej, zapytania systemu/role interfejsu API.

### <a name="object-types"></a>Typy obiektów

`ObjectIdType` Odwołuje się do typu tożsamości, daną rolę. Z wyjątkiem `DeviceId` i `UserDefinedFunctionId` typy, typy odpowiadają właściwości obiektu usługi Azure Active Directory (Azure AD):
  
* `UserId` Typu przypisuje rolę dla użytkownika.
* `DeviceId` Typu przypisuje rolę na urządzeniu.
* `DomainName` Typu przypisuje rolę, aby wskazywała nazwę domeny. Każdego użytkownika mającego określoną nazwę domeny ma prawa dostępu do odpowiedniej roli.
* `TenantId` Typu przypisuje rolę do dzierżawy. Każdy użytkownik, który należy do określonego Identyfikatora dzierżawy usługi Azure AD ma prawa dostępu do odpowiedniej roli.
* `ServicePrincipalId` Typ roli przypisuje identyfikator obiektu jednostki usługi.
* `UserDefinedFunctionId` Typu przypisuje rolę funkcji zdefiniowanej przez użytkownika (UDF).

> [!div class="nextstepaction"]
> [Zapytania lub identyfikator obiektu użytkownika](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0)

> [!div class="nextstepaction"]
> [Uzyskaj identyfikator obiektu dla nazwy głównej usługi](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.8.1)

> [!div class="nextstepaction"]
> [Pobieranie Identyfikatora obiektu dla dzierżawy usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)

## <a name="role-assignments"></a>Przypisania ról

Aby udzielić uprawnień do adresata, Utwórz przypisanie roli. Aby odwołać uprawnienia, usunąć przypisanie roli. Przypisanie roli Azure cyfrowego bliźniaczych reprezentacji kojarzy obiekt, na przykład użytkownik lub dzierżawę usługi Azure AD z roli i spację. Uprawnienia są przyznawane wszystkie obiekty, które należą do tego miejsca. Miejsce obejmuje cały wykres przestrzenne znajdujące się poniżej.

Na przykład, użytkownik otrzyma przypisania roli z rolą `DeviceInstaller` dla węzła głównego przestrzenne programu graph, która reprezentuje budynku. Użytkownik, a następnie może odczytywać i zaktualizuj urządzenia dla tego węzła i wszystkich innych podrzędnych miejsca do magazynowania w budynku.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o bezpieczeństwie Twins cyfrowych platformy Azure, przeczytaj [tworzenie i Zarządzanie przypisaniami ról](./security-create-manage-role-assignments.md).
