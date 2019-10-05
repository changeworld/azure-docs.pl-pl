---
title: Profil użytkownika i identyfikator do użycia z Azure Notebooks
description: Jak utworzyć profil użytkownika i identyfikator użytkownika oraz zarządzać nim przy użyciu Azure Notebooks.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 7d069d86-660f-4c94-b6e3-0c0f38c52d0e
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/25/2019
ms.author: kraigb
ms.openlocfilehash: 1fddefeb2a54ae775a9016799ffff1963eab247e
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970152"
---
# <a name="your-profile-and-user-id-for-azure-notebooks"></a>Twój profil i identyfikator użytkownika dla Azure Notebooks

W ramach zaawansowanego obszaru współpracy Azure Notebooks Twój profil użytkownika przedstawia publiczny obraz innym użytkownikom:

[Strona profilu Azure Notebooks @no__t 1An](media/accounts/profile-page.png)](media/accounts/profile-page.png#lightbox)

Identyfikator użytkownika jest częścią adresów URL używanych do udostępniania projektów i notesów. Na poniższej liście opisano różne wzorce adresów URL:

- `https://notebooks.azure.com/<user_id>`: Twoja strona Twojego profilu.
- `https://notebooks.azure.com/<user_id>/projects`: Twoje projekty. Zobaczysz wszystkie projekty; inni użytkownicy widzą tylko Twoje projekty publiczne.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>`: pliki projektu.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/clones`: klony określonych projektów.
- `https://notebooks.azure.com/<user_id>/projects/<project_id>/html/<notebook>.ipynb`: Podgląd HTML określonego notesu lub pliku.

## <a name="your-user-id"></a>Identyfikator użytkownika

Po pierwszym zalogowaniu się do Azure Notebooks Twoje konto zostanie automatycznie przypisane do tymczasowego identyfikatora użytkownika, takiego jak "Anon-idr3ca". O ile użytkownik ma identyfikator użytkownika zaczynający się od ciągu "Anon-", Azure Notebooks prosi o zmianę przy każdym zalogowaniu:

![Monituj o utworzenie identyfikatora użytkownika podczas logowania do Azure Notebooks](media/accounts/create-user-id.png)

W polu Nazwa użytkownika tymczasowego pojawia się również polecenie **Konfiguruj identyfikator użytkownika** :

![Skonfiguruj polecenie identyfikatora użytkownika, które pojawia się, gdy używasz identyfikatora tymczasowego](media/accounts/configure-user-id-command.png)

Identyfikator użytkownika można również zmienić w dowolnym momencie na stronie Twojego profilu.

Identyfikator użytkownika musi składać się z czterech i szesnastu liter, cyfr i łączników. Nie są dozwolone żadne inne znaki, a identyfikator użytkownika nie może rozpoczynać się ani kończyć łącznikiem ani używać wielu łączników w wierszu. Ponieważ identyfikatory użytkowników są unikatowe dla wszystkich kont Azure Notebooks, może zostać wyświetlony komunikat "identyfikator użytkownika jest już w użyciu". (Komunikat jest również wyświetlany, jeśli próbujesz użyć znaku towarowego firmy Microsoft jako identyfikatora użytkownika). W takich przypadkach wybierz inny identyfikator użytkownika.

> [!Important]
> Zmiana identyfikatora unieważnia wszystkie adresy URL, które mogły zostać udostępnione przy użyciu poprzedniego identyfikatora. Identyfikator można zmienić na poprzedni identyfikator, aby ponownie sprawdzić poprawność linków. Możliwe jest jednak, aby inny użytkownik zatwierdził nieużywany identyfikator w międzyczasie.

## <a name="your-profile"></a>Twój profil

Profil składa się z publicznie dodanych informacji w adresie URL, `https://notebooks.azure.com/<user_id>`. Na stronie profil znajdują się także ostatnio używane projekty i wszystkie projekty oznaczona gwiazdkami.

Aby edytować profil, użyj polecenia **Edytuj informacje o profilu** na stronie Twojego profilu. Poniżej przedstawiono sekcje profilu:

| Sekcja | Zawartość |
| --- | --- |
| Zdjęcie profilu | Obraz wyświetlany na stronie Twojego profilu. |
| Informacje o koncie | Nazwa wyświetlana, identyfikator użytkownika i publiczne konto e-mail. Konto e-mail w tym miejscu daje innym użytkownikom średnią skontaktowanie się z Tobą i może się różnić od [konta](azure-notebooks-user-account.md) , którego używasz do logowania się do Azure Notebooks samego siebie. |
| Informacje o profilu | Twoje miejsce, firma, stanowisko, witryna sieci Web i Krótki opis siebie. |
| Profile społecznościowe | Identyfikatory usługi GItHub, Twitter i Facebook, jeśli chcesz je udostępnić. |
| Ustawienia prywatności | Program udostępnia dwa polecenia:<ul><li>**Eksportuj mój profil**: tworzy i pobiera plik *. zip* zawierający wszystkie informacje, które Azure Notebooks zapisywane w Twoim profilu, w tym zdjęcie, informacje o profilu i dzienniki zabezpieczeń.</li><li>**Usuń moje konto**: trwale usuwa wszystkie dane osobowe przechowywane w Azure Notebooks.</li></ul> |
| Włącz funkcje witryny | Pozwala kontrolować aspekty zachowania Azure Notebooks:<ul><li>**Ujednolicone frontony dla notesów**: umożliwiają szybsze uruchamianie notesu i lepszą trwałość.</li><li>**Domyślnie Uruchom program w JupyterLab**: domyślnie Azure Notebooks udostępnia prosty interfejs użytkownika, który jest odpowiedni dla większości użytkowników. JupyterLab zapewnia bogatszy, ale bardziej skomplikowany interfejs dla doświadczonych użytkowników.</li><li>**Witryna sieci Web vNext**: umożliwia korzystanie z nowoczesnego układu internetowego pokazanego w tej dokumentacji.</li></ul> |

## <a name="next-steps"></a>Następne kroki  

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie notesu Jupyter w celu wykonania regresji liniowej](tutorial-create-run-jupyter-notebook.md)
