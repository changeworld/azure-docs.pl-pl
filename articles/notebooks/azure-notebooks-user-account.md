---
title: Logowanie się do usługi Azure Notes Preview
description: Skonfiguruj swoje konto użytkownika dla notesów platformy Azure przy użyciu konta Microsoft lub konta służbowego.
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 9401a9e483f3f8db5af827f53937cb0f01b27a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646300"
---
# <a name="your-user-account-for-azure-notebooks-preview"></a>Twoje konto użytkownika w wersji Zapoznawczej notesów platformy Azure

Notesy platformy Azure można używać z lub bez logowania się za pomocą konta użytkownika:

- Bez logowania można tworzyć i uruchamiać notesy, ale nie można zachować notesów ani plików danych w ramach projektów. Użytkownicy, którzy otrzymują łącze do notesu platformy Azure, na przykład, mogą korzystać z notesu bez konieczności logowania się.
- Po zalogowaniu notesy platformy Azure zachowują wszystkie projekty za pomocą konta. Zalogowanych użytkowników mają również identyfikator użytkownika, który pozwala im udostępniać swoje projekty i notesy innym osobom.
- Gdy konto używane dla notesów platformy Azure jest również skojarzone z subskrypcją platformy Azure, można uzyskać dodatkowe korzyści, takie jak uruchamianie notesów na bardziej zaawansowanych serwerach, tworzenie notesów prywatnych i udzielanie uprawnień do notesów dla poszczególnych użytkowników.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Logowanie się do notesów platformy Azure wymaga konta Microsoft lub konta "Praca lub szkoła". Podczas wybierania polecenia Zaloguj się **w** prawym górnym rogu strony Notesy zostanie wyświetlony monit o wyświetlenie konta:

![Polecenie Zaloguj się dla notesów platformy Azure](media/accounts/sign-in-command.png)

Cała praca wykonynana w notesach platformy Azure jest skojarzona z kontem używanym do logowania. Każde konto musi mieć również unikatowy identyfikator użytkownika w [profilu użytkownika.](azure-notebooks-user-profile.md) W rezultacie można zalogować się do notesów platformy Azure z różnych kont, jeśli trzeba zachować oddzielne zestawy projektów i oddzielnych tożsamości. Na przykład każdy członek zespołu do nauki o danych może mieć zarówno indywidualne konta obok jako udostępnione konto grupy, które używają do prezentowania swojej pracy osobom spoza firmy. Instruktorzy, podobnie, może prowadzić konto dla ich roli nauczania, który różni się od konta używanego w zewnętrznych konsultacji lub pracy open-source.

## <a name="microsoft-accounts"></a>Konta Microsoft

Konta Microsoft są używane do logowania się do dowolnej liczby produktów i usług firmy Microsoft, takich jak Windows, Azure, outlook.com, OneDrive i XBox Live. Jeśli używasz dowolnej z tych usług, jest prawdopodobne, że masz już konto Microsoft, którego można używać z notesami platformy Azure.

Jeśli nie masz pewności, wybierz polecenie **Utwórz jeden** w wierszu konta. Nowe konto Microsoft można utworzyć przy użyciu dowolnego adresu e-mail od dowolnego dostawcy.

![Polecenie utworzenia nowego konta Microsoft](media/accounts/create-new-microsoft-account.png)

> [!Note]
> Jeśli spróbujesz utworzyć nowe konto z adresem e-mail, który ma już skojarzone konto, możesz zobaczyć komunikat "Nie możesz zarejestrować się tutaj przy roboczym lub szkolnym adresie e-mail. Użyj osobistej poczty e-mail, takiej jak Gmail lub Yahoo!, lub uzyskaj nową wiadomość e-mail programu Outlook." W takim przypadku spróbuj zalogować się przy za pomocą służbowego adresu e-mail bez tworzenia nowego konta.

W przypadku kont podrzędnych dostęp do notesów platformy Azure jest domyślnie blokowany. Logowanie się za pomocą konta podrzędnego powoduje wyświetlenie błędu pokazanego poniżej:

![Błąd podczas próby zalogowania się przy za pomocą konta dziecka: coś poszło nie tak, rodzic zablokował dostęp](media/accounts/child-account-error.png)

Aby włączyć dostęp, rodzic musi wykonać następujące czynności:

1. Odwiedź `https://account.live.com/mk` konto rodzica i zaloguj się.
1. W sekcji dla danego dziecka wybierz pozycję **Zarządzaj dostępem tego dziecka do aplikacji innych firm.**
1. Na następnej stronie wybierz pozycję **Włącz dostęp**.
1. Gdy konto podrzędne jest obok używane do logowania się do notesów platformy Azure, wybierz **tak** w wyświetlonym prosze uprawnień.

> [!Warning]
> Włączenie dostępu do aplikacji innych firm dla notesów platformy Azure umożliwia również dostęp do wszystkich innych aplikacji innych firm. Rodzice powinni zachować dyskrecję podczas włączania dostępu i mogą chcieć dokładniej monitorować aktywność dziecka.

## <a name="work-or-school-accounts"></a>Konta służbowe

Administrator organizacji tworzy konto służbowe, aby umożliwić członkowi instytucji dostęp do usług w chmurze firmy Microsoft, takich jak Office 365, a także jako konto do logowania się do systemu Windows na komputerze przyłączanym do domeny. Konto służbowe zazwyczaj używa organizacyjnego adresu e-mail, takiego jak any-user@contoso.com.

Logowanie się do notesów platformy Azure za pomocą konta służbowego może wymagać zgody administratora, ponieważ notesy platformy Azure zbierają lub używają (ale nie ujawniają) informacji, takich jak adres e-mail konta i informacje przeglądarki użytkownika. (Dane przeglądarki służą do optymalizacji funkcji w zależności od popularnego użycia).

Administrator konta organizacji musi wyrazić zgodę w imieniu użytkowników, jeśli użytkownicy nie mogą udzielać zgody indywidualnie. W takim przypadku użytkownicy widzą komunikat "Nie można uzyskać dostępu do tej aplikacji":

![Komunikat "Nie możesz uzyskać dostępu do tej aplikacji" podczas korzystania z konta służbowego](media/accounts/consent-permissions-denied.png)

Aby wyrazić zgodę jako administrator, użyj [strony zgody administratora](https://notebooks.azure.com/account/adminConsent), która przeprowadzi Cię przez proces.

## <a name="next-steps"></a>Następne kroki  

> [!div class="nextstepaction"]
> [Edytowanie profilu i identyfikatora użytkownika](azure-notebooks-user-profile.md)
