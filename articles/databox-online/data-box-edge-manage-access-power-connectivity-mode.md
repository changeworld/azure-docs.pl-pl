---
title: Dostęp urządzeń w usłudze Azure krawędź pola danych, moc i tryb łączności | Dokumentacja firmy Microsoft
description: Opisuje sposób zarządzania dostęp, moc i tryb łączności dla urządzenia usługi Azure Data Box Edge, że ułatwia przesyłanie danych do platformy Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: 8937f4c47f0fa84d4ec371e951cff8a2fdaa8481
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66476903"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-edge"></a>Zarządzanie dostępem, power i tryb łączności na krawędzi sieci Azure Data Box

W tym artykule opisano sposób zarządzania trybu dostępu, moc i łączności na krawędzi sieci Azure Data Box. Te operacje są wykonywane za pomocą lokalnego Interfejsu w przeglądarce lub w portalu Azure.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Zarządzanie dostępem do urządzeń
> * Zarządzanie tryb łączności
> * Zarządzanie energią


## <a name="manage-device-access"></a>Zarządzanie dostępem do urządzeń

Dostęp do urządzenia usługi Edge pole danych jest kontrolowany przy użyciu hasła urządzenia. Można zmienić hasło przy użyciu lokalnego Interfejsu w przeglądarce. Mogą również resetować hasła urządzenia w witrynie Azure portal.

### <a name="change-device-password"></a>Zmienianie hasła urządzenia

Wykonaj następujące kroki w interfejsie użytkownika lokalnego, aby zmienić hasło urządzenia.

1. W lokalnym internetowym interfejsie użytkownika, przejdź do **konserwacji > zmiany hasła**.
2. Wprowadź bieżące hasło, a następnie nowe hasło. Podane hasło musi mieć długość od 8 do 16 znaków. Hasło musi zawierać 3 z następujących znaków: wielkie litery, małe litery, cyfry i znaki specjalne. Potwierdź nowe hasło.

    ![Zmień hasło](media/data-box-edge-manage-access-power-connectivity-mode/change-password-1.png)

3. Wybierz **Zmień hasło**.
 
### <a name="reset-device-password"></a>Resetowanie hasła urządzenia

Resetowanie przepływ pracy wymaga od użytkownika przywołać stare hasło i jest przydatne, gdy hasło zostanie utracone. Ten przepływ pracy jest wykonywane w witrynie Azure portal.

1. W witrynie Azure portal przejdź do **Przegląd > Resetuj hasło administratora**.

    ![Resetowanie hasła](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-1.png)


2. Wprowadź nowe hasło, a następnie potwierdź je. Podane hasło musi mieć długość od 8 do 16 znaków. Hasło musi zawierać 3 z następujących znaków: wielkie litery, małe litery, cyfry i znaki specjalne. Wybierz **resetowania**.

    ![Resetowanie hasła](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Zarządzanie dostępem do zasobów

Aby utworzyć usługi bramy okno usługi Edge i dane pole danych, usługi IoT Hub i zasobu usługi Azure Storage, musisz mieć uprawnienia, jako współautora lub nowszej na poziomie grupy zasobów. Należy również odpowiednich dostawców zasobów do zarejestrowania. Wszelkie operacje, które obejmują klucz aktywacji i poświadczenia wymagane są również uprawnienia do interfejsu API usługi Azure Active Directory Graph. Te ustawienia zostały opisane w poniższych sekcjach.

### <a name="manage-microsoft-azure-active-directory-graph-api-permissions"></a>Zarządzanie uprawnieniami Microsoft Azure Active Directory interfejsu API programu Graph

Podczas generowania klucza aktywacji urządzenia krawędź pola danych lub wykonywanie żadnych operacji wymagających poświadczeń, musisz mieć uprawnienia do interfejsu API usługi Azure Active Directory Graph. Operacje, które są wymagane poświadczenia mogą być:

-  Tworzenie udziału ze skojarzonego konta magazynu.
-  Tworzenie użytkowników, którzy mogą uzyskiwać dostęp do udziałów na urządzeniu.

Musisz mieć `User` dostępu w dzierżawie usługi Active Directory, ponieważ muszą mieć możliwość `Read all directory objects`. Nie może być użytkownikiem-gościem, ponieważ nie mają uprawnień do `Read all directory objects`. Jeśli jesteś gościa, a następnie operacje, takie jak generowanie klucza aktywacji tworzenia udziału na urządzeniu z systemem krawędź pola danych, tworzenia użytkownika wszystkie powiedzie się.

Aby uzyskać więcej informacji na temat jak zapewnić dostęp użytkowników do interfejsu API usługi Azure Active Directory Graph, zobacz [domyślne dostępu dla administratorów, użytkowników i użytkowników-gości](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).

### <a name="register-resource-providers"></a>Rejestrowanie dostawcy zasobów

Aby udostępnić zasób na platformie Azure (w modelu usługi Azure Resource Manager), należy dostawcy zasobów, który obsługuje tworzenie tego zasobu. Na przykład aby udostępnić maszynę wirtualną, należy dostawcy zasobów "Microsoft.Compute" dostępnych w ramach subskrypcji.
 
Dostawcy zasobów są rejestrowane na poziomie subskrypcji. Domyślnie wszystkie nowej subskrypcji platformy Azure jest wstępnie zarejestrowane przy użyciu listy dostawców powszechnie używanych zasobów. Dostawca zasobów dla "Microsoft.DataBoxEdge" nie znajduje się na tej liście.

Nie musisz udzielić uprawnień dostępu do poziomu subskrypcji dla użytkowników można było utworzyć zasobów, takich jak "Microsoft.DataBoxEdge" w obrębie swojej grupy zasobów, które mają prawa właściciela, tak długo, jak dostawców zasobów dla tych zasobów jest już zarejestrowany.

Przed przystąpieniem do tworzenia wszystkich zasobów, upewnij się, że dostawca zasobów jest zarejestrowany w ramach subskrypcji. Dostawca zasobów nie jest zarejestrowany, należy się upewnić, że użytkownik tworzący nowy zasób ma wystarczających praw, aby zarejestrować dostawcę wymaganych zasobów na poziomie subskrypcji. Jeśli nie masz jeszcze ustanowionego także, zostanie wyświetlony następujący błąd:

*Subskrypcja <Subscription name> nie ma uprawnień do rejestrowania dostawców zasobów: Microsoft.DataBoxEdge.*


Aby uzyskać listę dostawców zarejestrowanych zasobów w bieżącej subskrypcji, uruchom następujące polecenie:

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

Dla urządzenia krawędź pola danych `Microsoft.DataBoxEdge` powinny być rejestrowane. Aby zarejestrować `Microsoft.DataBoxEdge`, administrator subskrypcji należy uruchomić następujące polecenie:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

Aby uzyskać więcej informacji na temat sposobu rejestrowania dostawcy zasobów, zobacz [rozwiązać błędy rejestracji dostawcy zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="manage-connectivity-mode"></a>Zarządzanie tryb łączności

Oprócz to domyślny tryb w pełni połączone urządzenia można również uruchomić w trybie połączonych częściowo lub całkowicie odłączony. Każde z tych trybów opisano poniżej:

- **W pełni połączone** — jest to normalne domyślny tryb, w której działa urządzenie. Chmura przekazywania i pobierania danych jest włączona w tym trybie. Witryna Azure portal lub lokalnego Interfejsu w przeglądarce służy do zarządzania urządzeniami.

- **Częściowo odłączony** — w tym trybie urządzenia nie można przekazywać i pobierać każdy udział dane jednak mogą być zarządzane za pośrednictwem witryny Azure portal.

    W tym trybie zwykle jest używana podczas sieci satelitarnych mierzonego i celem jest minimalizacja zużycie przepustowości sieci. Użycie sieci minimalnej nadal może być operacje monitorowania urządzeń.

- **Odłączony** — w tym trybie urządzenie zostanie całkowicie odłączony od chmury i zarówno w chmurze przekazywanie i pobieranie jest wyłączone. Urządzenia mogą być zarządzane tylko za pomocą lokalnego Interfejsu w przeglądarce.

    Ten tryb jest zwykle używany przełączyć w tryb offline urządzenia.

Aby zmienić tryb urządzenia, wykonaj następujące kroki:

1. W lokalnym internetowym interfejsie użytkownika, urządzenia, przejdź do **Konfiguracja > Ustawienia funkcji Cloud**.
2. Z listy rozwijanej wybierz tryb, w którym mają być używane urządzenia w środowisku. Możesz wybrać z **nawiązane pełne połączenie**, **częściowo połączone**, i **całkowicie odłączony**. Aby uruchomić urządzenie w trybie rozłączonym częściowo, Włącz **Azure portal management**.

    ![Tryb łączności](media/data-box-edge-manage-access-power-connectivity-mode/connectivity-mode.png)
 
## <a name="manage-power"></a>Zarządzanie energią

Można zamknąć lub ponowne uruchomienie urządzenia fizycznego przy użyciu lokalnego Interfejsu w przeglądarce. Firma Microsoft zaleca się, że przed ponownym uruchomieniem, przełączyć udziały w tryb offline na serwerze danych, a następnie urządzenia. Ta akcja minimalizuje możliwości uszkodzenie danych.

1. W lokalnym internetowym interfejsie użytkownika, przejdź do **konserwacji > Ustawienia zasilania**.
2. Wybierz **zamknięcia** lub **ponowne uruchomienie** w zależności od tego, co zamierzasz wykonać.

    ![Ustawienia zasilania](media/data-box-edge-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Po wyświetleniu monitu o potwierdzenie, wybierz **tak** aby kontynuować.

> [!NOTE]
> Wyłączenie urządzenia fizycznego należy naciśnij przycisk zasilania na urządzeniu, aby włączyć tę funkcję.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [Zarządzanie udziałami](data-box-edge-manage-shares.md).