---
title: Understanding Azure cyfrowego bliźniaczych reprezentacji opartej na rolach kontrola dostępu | Dokumentacja firmy Microsoft
description: Dowiedz się, uwierzytelnianie w reprezentacji urządzeń cyfrowych przy użyciu kontroli dostępu opartej na rolach.
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: lyrana
ms.openlocfilehash: 7a6d8565a0f85b4cb81d9f5f23b04fe6b2edc53e
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324218"
---
# <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Twins cyfrowych platformy Azure umożliwia kontrolowanie precyzyjnego dostępu do określonych danych, zasobów i akcji w grafie przestrzennych. Robi to za pośrednictwem szczegółowe zarządzanie rolami i uprawnieniami o nazwie _kontroli dostępu opartej na rolach_. Kontrola dostępu oparta na rolach składa się z _role_, lub poziom uprawnień, a _przypisań ról_, lub skojarzenie roli użytkownika lub urządzenia.

Za pomocą kontroli dostępu opartej na rolach, uprawnień można udzielić do użytkownika, urządzenia, nazwę główną usługi, funkcji zdefiniowanych przez użytkownika, wszyscy użytkownicy należący do domeny lub dzierżawy. Ponadto stopnia dostępu można też dostosować.

Kontrola dostępu oparta na rolach jest unikatowa, w tym, że uprawnienia są dziedziczone w dół przestrzenne programu graph.

## <a name="what-can-i-do-with-role-based-access-control"></a>Co można zrobić przy użyciu kontroli dostępu opartej na rolach?

Deweloper może użyć opartej na rolach kontrola dostępu do:

* Udzielić użytkownikowi możliwość zarządzania urządzeniami dla całego kompilowania lub tylko dla konkretnego pomieszczenia lub piętra.
* Udziel administrator globalny dostęp do wszystkich węzłów przestrzenne wykresu cały wykres, czy tylko w części wykresu.
* Udzielanie dostępu Odczyt specjalista ds. pomocy technicznej do grafu, z wyjątkiem klucze dostępu.
* Przyznaj każdy członek domeny do odczytu wszystkich obiektów grafu.

## <a name="role-based-access-control-best-practices"></a>Najlepszymi praktykami kontroli dostępu opartej na rolach

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>Role

### <a name="role-definitions"></a>Definicje ról

A **definicji roli** to zbiór uprawnień i jest czasami nazywane **roli**. Definicja roli zawiera dozwolone operacje, w tym *tworzenie*, *odczytu*, *aktualizacji*, i *Usuń*. Określa również, typy obiektów, których dotyczą te uprawnienia.

Następujące role są dostępne w reprezentacji urządzeń cyfrowych platformy Azure:

* **Administrator miejsca**: tworzenia, odczytu, aktualizacji i usuwania uprawnień do określonego miejsca i na wszystkich węzłach poniżej. Uprawnienie globalne.
* **Administrator użytkowników**: tworzenia, odczytu, aktualizacji i usuwania uprawnień dla użytkowników i obiektów związanych z użytkownika. Uprawnienia do odczytu dla miejsca do magazynowania.
* **Rola Administrator urządzenia**: tworzenia, odczytu, aktualizacji i usuwania uprawnień dla urządzeń i obiektów związanych z urządzenia. Uprawnienia do odczytu dla miejsca do magazynowania.
* **Klucz administratora**: tworzenia, odczytu, aktualizacji i usuwania uprawnień dostępu do kluczy. Uprawnienia do odczytu dla miejsca do magazynowania.
* **Token administratora**: uprawnienia odczytu i aktualizacji dla kluczy dostępu. Uprawnienia do odczytu dla miejsca do magazynowania.
* **Użytkownik**: uprawnienia do odczytu miejsca do magazynowania, czujniki i użytkowników, w tym odpowiadające im dotyczących obiektów.
* **Specjalista ds. obsługi**: uprawnienia do wszystkim, z wyjątkiem kluczy dostępu do odczytu.
* **Instalator urządzenia**: uprawnienia odczytu i aktualizacji dla urządzeń i czujników, takich jak odpowiadające im powiązanych obiektów. Uprawnienia do odczytu dla miejsca do magazynowania.
* **Urządzenie bramy**: Utwórz uprawnienie do czujników. Uprawnienia do odczytu urządzeń i czujników, odpowiadające im w tym powiązane obiekty.

>[!NOTE]
> *Pełne definicje dla powyższych mogą być pobierane przez wysyłanie zapytań / sytemu interfejsu API.*

### <a name="object-types"></a>Typy obiektów

`ObjectIdType` Odwołuje się do typu tożsamości, która otrzymuje jest rolą. Z wyjątkiem `DeviceId` i `UserDefinedFunctionId` typy, typy odpowiadają właściwości obiektu usługi Azure Active Directory (Azure AD):
  
* `UserId` Typu przypisuje rolę dla użytkownika.
* `DeviceId` Typu przypisuje rolę na urządzeniu.
* `DomainName` Typu przypisuje rolę, aby wskazywała nazwę domeny. Każdego użytkownika mającego określoną nazwę domeny będą mieć prawa dostępu do odpowiedniej roli.
* `TenantId` Typu przypisuje rolę do dzierżawy. Każdy użytkownik należący do określonego Identyfikatora dzierżawy usługi Azure AD będą mieć prawa dostępu do odpowiedniej roli.
* `ServicePrincipalId` Typ roli przypisuje identyfikator obiektu jednostki usługi.
* `UserDefinedFunctionId` Typu przypisuje rolę do funkcji zdefiniowane przez użytkownika (UDF).

> [!div class="nextstepaction"]
> [Zapytania lub identyfikator obiektu użytkownika](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0)

> [!div class="nextstepaction"]
> [Uzyskaj identyfikator obiektu dla nazwy głównej usługi](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.8.1)

> [!div class="nextstepaction"]
> [Pobieranie Identyfikatora obiektu dla dzierżawy usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)

## <a name="role-assignments"></a>Przypisania ról

Uprawnienia są przyznane do adresata, tworząc przypisania roli i odwołane przez usunięcie przypisania roli. Przypisanie roli Azure cyfrowego bliźniaczych reprezentacji kojarzy obiekt (użytkownika, dzierżawy usługi Azure AD, itp.), roli i spację. Następnie uprawnienia są przyznawane wszystkie obiekty, które należą do tego miejsca, w tym cały wykres przestrzenne znajdujące się poniżej.

Na przykład, użytkownik otrzyma przypisania roli z rolą `DeviceInstaller` dla węzła głównego przestrzenne programu graph, która reprezentuje budynku. Następnie użytkownik będzie mogła odczytywać i aktualizować urządzeń nie tylko dla tego węzła, ale inne podrzędne przestrzenie w budynku.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o bezpieczeństwie Twins cyfrowych platformy Azure, przeczytaj [tworzenie i Zarządzanie przypisaniami ról](./security-create-manage-role-assignments.md).
