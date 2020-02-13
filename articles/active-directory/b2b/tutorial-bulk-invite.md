---
title: Samouczek dotyczący zbiorczego zapraszania użytkowników współpracy B2B — Azure AD
description: Z tego samouczka dowiesz się, jak wysyłać zaproszenia zbiorcze do zewnętrznych użytkowników współpracy w usłudze Azure AD B2B za pomocą programu PowerShell i pliku CSV.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 2/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: c429648adeb0c81799bff2dca1650de965395a60
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77166452"
---
# <a name="tutorial-bulk-invite-azure-ad-b2b-collaboration-users-preview"></a>Samouczek: zbiorcze Zapraszanie użytkowników współpracy B2B usługi Azure AD (wersja zapoznawcza)

|     |
| --- |
| W tym artykule opisano publiczną funkcję w wersji zapoznawczej programu Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz temat [Dodatkowe warunki użytkowania dotyczące wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

> [!NOTE]
> Od 12/22/2019 funkcja zbiorczych zaproszonych użytkowników (wersja zapoznawcza) została tymczasowo wyłączona.
> Nie ma obecnie znanej daty, gdy ta funkcja zostanie włączona w Azure Portal. Aby zapraszać użytkowników-Gości w sposób zbiorczy przy użyciu programu PowerShell, zobacz [Samouczek dotyczący zaproszeń zbiorczych B2B](bulk-invite-powershell.md) lub [kod B2B oraz przykłady programu PowerShell](code-samples.md).

Jeśli używasz funkcji współpracy B2B w usłudze Azure Active Directory (Azure AD) do pracy z partnerami zewnętrznymi, możesz równocześnie zapraszać wielu użytkowników-gości do swojej organizacji. W ramach tego samouczka nauczysz się używać Azure Portal do wysyłania zaproszeń zbiorczych do użytkowników zewnętrznych. W szczególności należy wykonać następujące czynności:

> [!div class="checklist"]
> * Użyj **zbiorczych zaproszonych użytkowników (wersja zapoznawcza)** w celu przygotowania pliku z wartościami rozdzielanymi przecinkami (CSV) za pomocą preferencji informacji o użytkowniku i zaproszenia
> * Przekazywanie pliku CSV do usługi Azure AD
> * Sprawdzenie, czy użytkownicy zostali dodani do katalogu

Jeśli nie masz Azure Active Directory, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) . 

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebujesz co najmniej dwóch testowych kont e-mail, na które będzie można wysłać zaproszenia. Konta muszą być spoza Twojej organizacji. Możesz użyć dowolnego typu konta, w tym kont społecznościowych z adresem w domenie gmail.com lub outlook.com.

## <a name="invite-guest-users-in-bulk"></a>Zapraszanie użytkowników-Gości w trybie zbiorczym

1. Zaloguj się do Azure Portal przy użyciu konta, które jest administratorem użytkownika w organizacji.
2. W okienku nawigacji wybierz pozycję **Azure Active Directory**.
3. W obszarze **Zarządzanie**wybierz pozycję **Użytkownicy** > **Zaproś zbiorczy**.
4. Na stronie **Zaproś użytkowników (wersja zapoznawcza)** wybierz pozycję **Pobierz** , aby pobrać prawidłowy plik CSV z właściwościami zaproszenia.

    ![Przycisk pobierania zaproszeń zbiorczych](media/tutorial-bulk-invite/bulk-invite-button.png)

5. Otwórz plik CSV i Dodaj wiersz dla każdego użytkownika-gościa. Wymagane wartości to:

   * **Adres e-mail do zaproszenia** — użytkownik, który otrzyma zaproszenie

   * **Adres URL przekierowania** — adres URL, do którego zostanie przekazany zaproszony użytkownik po zaakceptowaniu zaproszenia

    ![Przykładowy plik CSV z wprowadzonymi użytkownikami gościa](media/tutorial-bulk-invite/bulk-invite-csv.png)

   > [!NOTE]
   > Nie używaj przecinków w **dostosowywanym komunikacie zaproszenia** , ponieważ uniemożliwi to pomyślne przeanalizowanie komunikatu.

6. Zapisz plik.
7. Na stronie **zbiorcze Zapraszanie użytkowników (wersja zapoznawcza)** w obszarze **Przekaż plik CSV**przejdź do pliku. Po wybraniu pliku zostanie uruchomiony Walidacja pliku CSV. 
8. Gdy zawartość pliku zostanie sprawdzona, zostanie wyświetlony **plik przekazany pomyślnie**. Jeśli występują błędy, należy je usunąć przed przesłaniem zadania.
9. Gdy plik zostanie pomyślnie zweryfikowany, wybierz pozycję **Prześlij** , aby rozpocząć operację zbiorczą platformy Azure, która dodaje zaproszenia. 
10. Aby wyświetlić stan zadania, wybierz **pozycję kliknij tutaj, aby wyświetlić stan każdej operacji**. Można też wybrać pozycję **wyniki operacji zbiorczej (wersja zapoznawcza)** w sekcji **działanie** . Aby uzyskać szczegółowe informacje na temat każdego elementu wiersza w ramach operacji zbiorczej, wybierz wartości w kolumnach **# Success**, **# Failure**lub **Total Requests** . Jeśli wystąpią błędy, zostaną wyświetlone przyczyny niepowodzenia.

    ![Przykład wyników operacji zbiorczej](media/tutorial-bulk-invite/bulk-operation-results.png)

11. Po zakończeniu zadania zobaczysz powiadomienie, że operacja zbiorcza zakończyła się pomyślnie.

## <a name="verify-guest-users-in-the-directory"></a>Weryfikowanie użytkowników-Gości w katalogu

Sprawdź, czy dodani użytkownicy-Goście znajdują się w katalogu w Azure Portal lub przy użyciu programu PowerShell.

### <a name="view-guest-users-in-the-azure-portal"></a>Wyświetlanie użytkowników-Gości w Azure Portal

1. Zaloguj się do Azure Portal przy użyciu konta, które jest administratorem użytkownika w organizacji.
2. W okienku nawigacji wybierz pozycję **Azure Active Directory**.
3. W obszarze **Zarządzaj** wybierz pozycję **Użytkownicy**.
4. W obszarze **Pokaż**wybierz pozycję **tylko użytkownicy-Goście** i sprawdź, czy dodano użytkowników.

### <a name="view-guest-users-with-powershell"></a>Wyświetlanie użytkowników-Gości przy użyciu programu PowerShell

Uruchom następujące polecenie:

```powershell
 Get-AzureADUser -Filter "UserType eq 'Guest'"
```

Zobaczysz użytkowników, którzy zaprosił na liście, z główną nazwą użytkownika (UPN) w formacie *emailaddress*#EXT #\@*Domain*. Na przykład *lstokes_fabrikam. com # EXT #\@contoso.onmicrosoft.com*, gdzie contoso.onmicrosoft.com jest organizacją, z której wysłano zaproszenia.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, możesz usunąć konta użytkowników testowych z katalogu w Azure Portal na stronie użytkownicy, zaznaczając pole wyboru obok pola Gość użytkownika, a następnie wybierając pozycję **Usuń**. 

Możesz też uruchomić następujące polecenie programu PowerShell, aby usunąć konto użytkownika:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```

Na przykład: `Remove-AzureADUser -ObjectId "lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com"`

## <a name="next-steps"></a>Następne kroki

W tym samouczku wysłano zaproszenia zbiorcze do użytkowników-gości spoza organizacji. Następnie dowiesz się, jak działa proces realizacji zaproszenia.

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat procesu realizacji zaproszenia do współpracy B2B w usłudze Azure AD](redemption-experience.md)
