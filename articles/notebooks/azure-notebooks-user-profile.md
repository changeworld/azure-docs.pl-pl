---
title: Profil użytkownika i identyfikator do użycia z podglądem Azure Notebooks
description: Tworzenie profilu użytkownika i identyfikatora użytkownika oraz zarządzanie nim przy użyciu Azure Notebooks, który jest częścią adresu URL udostępnionych notesów.
ms.topic: conceptual
ms.date: 02/25/2019
ms.openlocfilehash: d90eebf1b7b463e038bc5e54f51df0eb6ca746c4
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646283"
---
# <a name="your-profile-and-user-id-for-azure-notebooks-preview"></a>Twój profil i identyfikator użytkownika dla Azure Notebooks wersja zapoznawcza

W obszarze Zaawansowane, współpracy notesów usługi Azure profilu użytkownika przedstawia publiczny obraz do innych osób:

[![strony profilu Azure Notebooks](media/accounts/profile-page.png)](media/accounts/profile-page.png#lightbox)

Identyfikator użytkownika jest częścią adresów URL umożliwia udostępnianie projektów i notesy. Poniższa lista zawiera opis różnych wzorców adresów URL:

- `https://notebooks.azure.com/<user_id>`: Stronę profilu.
- `https://notebooks.azure.com/<user_id>/projects`: Twoich projektów. Zobacz wszystkie projekty; inni użytkownicy widzą tylko projekty publiczne.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>`: Pliki projektu.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/clones`: Klony określonych projektów.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/html/<notebook>.ipynb`: HTML podglądu pliku lub określonym notesu.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="your-user-id"></a>Identyfikator użytkownika

Podczas logowania się do notesów usługi Azure po raz pierwszy, Twoje konto jest automatycznie przypisywany Identyfikatora tymczasowe użytkownika, takie jak "anon-idr3ca". Tak długo, jak długo ma identyfikator użytkownika, który rozpoczyna się od "anon-", notesy platformy Azure monituje o go zmienić po każdym zalogowaniu:

![Monit o utworzenie Identyfikatora użytkownika podczas logowania się do notesów usługi Azure](media/accounts/create-user-id.png)

A **skonfigurować identyfikator użytkownika** polecenie również pojawia się obok nazwy tymczasowych użytkowników:

![Konfigurowanie polecenia identyfikator użytkownika, który jest wyświetlany, gdy używasz tymczasowy identyfikator](media/accounts/configure-user-id-command.png)

Można również zmienić swój identyfikator użytkownika w dowolnym momencie na stronie profilu.

Identyfikator użytkownika musi składać się z czterech i szesnastu liter, cyfr i łączników. Inne znaki są niedozwolone, a identyfikator użytkownika nie można rozpocząć lub kończyć znakiem łącznika ani używać wiele łączników w wierszu. Ponieważ identyfikatory użytkowników są unikatowe dla wszystkich kont Azure Notebooks, może zostać wyświetlony komunikat "identyfikator użytkownika jest już w użyciu". (Komunikat jest również wyświetlany, jeśli próbujesz użyć znaku towarowego firmy Microsoft jako identyfikatora użytkownika). W takich przypadkach wybierz inny identyfikator użytkownika.

> [!Important]
> Zmiana Identyfikatora unieważnia wszelkie adresy URL mogą zostały udostępnione przy użyciu swojego identyfikatora poprzedniego. Możesz zmienić swój identyfikator powrót do poprzedniego Identyfikatora w celu ponownego zweryfikowania łącza. Jednak jest możliwe dla innego użytkownika skorzystać z nieużywane Identyfikatora, w tym samym czasie.

## <a name="your-profile"></a>Twój profil

Twój profil składa się z dostępne publicznie informacji pod adresem URL, `https://notebooks.azure.com/<user_id>`. Strony swojego profilu pokazuje również niedawno używanych projektów i wszelkie projekty gwiazdkami.

Aby edytować swój profil, należy użyć **edytować informacje o profilu** polecenia na stronie profilu. Części Twojego profilu są następujące:

| Sekcja | Zawartość |
| --- | --- |
| Zdjęcie profilowe | Obraz, który jest wyświetlany na stronie profilu. |
| Informacje o koncie | Nazwa wyświetlana, identyfikator użytkownika i konto e-mail publicznych. Na konto poczty e-mail zawiera mean skontaktowania się z innym użytkownikom i może różnić się od [konta](azure-notebooks-user-account.md) używasz do logowania się do notesów usługi Azure, sam. |
| Informacje o profilu | Twoja lokalizacja, firmy, stanowisko, witryny sieci web i krótki opis samodzielnie. |
| Profile społecznościowych | GItHub, Twitter i Facebook identyfikatory, jeśli chcesz udostępnić je. |
| Ustawienia prywatności | Udostępnia dwa polecenia:<ul><li>**Eksportuj Mój profil**: powoduje utworzenie i pobranie *zip* plik zawierający wszystkie informacje, które notesy platformy Azure są zapisywane w profilu, w tym Twoje zdjęcie, informacje o profilu i dzienniki zabezpieczeń.</li><li>**Usuń Moje konto**: trwale usuwa wszystkie informacje osobiste przechowywane w notesach platformy Azure.</li></ul> |
| Włącz funkcje lokacji | Umożliwia kontrolowanie aspektów zachowania notesy platformy Azure:<ul><li>**Ujednoliconego frontonu dla notesów**: umożliwia szybsze uruchamianie notesu i lepszego stanu trwałego.</li><li>**Domyślnie uruchamiane w JupyterLab**: domyślnie notesy platformy Azure udostępnia prosty interfejs użytkownika, który jest odpowiedni dla większości użytkowników. JupyterLab zapewnia bardziej rozbudowane, ale bardziej skomplikowanych interfejs dla doświadczonych użytkowników.</li><li>**Witryny sieci Web VNext**: włącza układ zmodernizowane sieci web, pokazana w tej dokumentacji.</li></ul> |

## <a name="next-steps"></a>Następne kroki  

> [!div class="nextstepaction"]
> [Samouczek: tworzenie uruchomienia notesu programu Jupyter w celu regresji liniowej](tutorial-create-run-jupyter-notebook.md)
