---
title: Zaloguj się do notesy platformy Azure
description: Skonfiguruj Twoje konto użytkownika dla notesów usługi Azure przy użyciu konta Microsoft lub konto służbowe.
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: a2d8c8180dfb5dc31e273c7953a57f40cf31238d
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277619"
---
# <a name="your-user-account-for-azure-notebooks"></a>Twoje konto użytkownika dla notesów usługi Azure

Notesy platformy Azure można użyć z lub bez niej zalogować się przy użyciu konta użytkownika:

- Bez rejestrowania się w tworzenie i uruchamianie notesów, ale nie można zachować notesów lub pliki danych w ramach projektów. Użytkownicy, którzy otrzymają link do notesu platformy Azure, na przykład, cieszyć się Notes bez potrzeby logowania się.
- Po zalogowaniu notesów usługi Azure zachowuje wszystkie projekty z Twoim kontem. Zalogowanych użytkowników mają również identyfikator użytkownika, która umożliwia im ją udostępnić swoje projekty i notesy.
  - Jeśli konto używane do notesów usługi Azure jest również skojarzony z subskrypcją platformy Azure, uzyskasz dodatkowe korzyści, takie jak uruchomienie notesów na serwerach bardziej wydajne Tworzenie prywatnego notesów i udzielanie uprawnień do notesów dla poszczególnych użytkowników.

Logując się do platformy Azure, notesy wymaga Account Microsoft lub konta "Pracy lub szkoły". Po wybraniu polecenia **Zaloguj** w prawym górnym rogu strony Notes zostanie wyświetlony monit o podanie konta:

![Zaloguj się w poleceniu dla notesów usługi Azure](media/accounts/sign-in-command.png)

Cała praca wykonywana w notesach Azure jest skojarzony z kontem, którego używasz do logowania. Każde konto musi mieć unikatowy identyfikator użytkownika w [profilu użytkownika](azure-notebooks-user-profile.md). Co w efekcie użytkownik może zalogować się do notesów usługi Azure przy użyciu różnych kont Jeśli trzeba utrzymywać oddzielnych zestawów projektów i osobne tożsamości. Na przykład każdy członek zespołu do nauki o danych może mieć zarówno indywidualnych kont, wraz z konta grupę udostępnioną, które mogą służyć do prezentowania swoją pracę osobom spoza firmy. Podobnie, instruktorów, może utrzymywać konto w ramach swojej roli nauczania, który różni się od konta używane w zewnętrznych konsultacji lub pracy typu open source.

## <a name="microsoft-accounts"></a>Konta Microsoft

Konta Microsoft są używane do logowania się do dowolnej liczby produktów i usług, takich jak Windows Azure, outlook.com, OneDrive i XBox Live. Jeśli używasz dowolnego z tych usług, prawdopodobnie już Account Microsoft za pomocą notesów usługi Azure.

Jeśli nie masz pewności, wybierz polecenie **Utwórz jedno** w monicie konta. Można utworzyć nowe konto Microsoft, przy użyciu dowolnego adresu e-mail od dowolnego dostawcy.

![Polecenie, aby utworzyć nowe konto Microsoft](media/accounts/create-new-microsoft-account.png)

> [!Note]
> Jeśli spróbujesz utworzyć nowe konto przy użyciu adresu e-mail, z którym jest już skojarzone konto, może zostać wyświetlony komunikat "nie można utworzyć konta w tym miejscu przy użyciu służbowego adresu e-mail. Użyj osobistej poczty e-mail, takiej jak Gmail lub Yahoo!, lub Utwórz nową wiadomość e-mail w programie Outlook. W takim przypadku spróbuj zalogować się przy użyciu służbowego adresu e-mail bez tworzenia nowego konta.

Dla kont dzieci dostęp do notesów usługi Azure jest blokowany domyślnie. Logowanie się przy użyciu konta dziecka wyświetla błąd pokazany poniżej:

![Wystąpił błąd podczas próby Zaloguj się przy użyciu konta dziecka: Niestety, wystąpił problem, Twój rodzic zablokował dostęp](media/accounts/child-account-error.png)

Aby włączyć dostęp, element nadrzędny, należy wykonać następujące czynności:

1. Odwiedź stronę `https://account.live.com/mk` i zaloguj się przy użyciu konta nadrzędnego.
1. W sekcji dla danego elementu podrzędnego wybierz pozycję **Zarządzaj dostępem tego elementu podrzędnego do aplikacji innych**firm.
1. Na następnej stronie wybierz pozycję **Włącz dostęp**.
1. Po następnym użyciu konta podrzędnego do zalogowania się do Azure Notebooks wybierz pozycję **tak** w wyświetlonym monicie o uprawnienia.

> [!Warning]
> Włączanie dostępu do aplikacji innych firm dla notesów usługi Azure umożliwia również dostęp do wszystkich innych aplikacjach innych firm. Elementy nadrzędne doradza się należy zachować ostrożność podczas włączania dostępu i może być bardziej ściśle monitorować działanie ich podrzędne.

## <a name="work-or-school-accounts"></a>Konta służbowe

Konto służbowe jest tworzony przez administratora organizacji i członkowi organizacji do dostępu do usług chmurowych firmy Microsoft, takich jak Office 365, a także jako konta do logowania się do Windows na komputerze przyłączonym do domeny. Konto służbowe zwykle używa adresu e-mail organizacji, takiego jak any-user@contoso.com.

Zalogowanie notesy platformy Azure przy użyciu konta służbowego lub szkolnego może wymagają zgody administratora, ponieważ notesy platformy Azure umożliwia zbieranie informacji o lub korzysta z (ale nie ujawniają) informacje, takie jak adres e-mail konta i informacje o przeglądarce użytkownika. (Dane przeglądarki jest używany optymalizację funkcji zgodnie z popularnych użycia).

Administrator konta organizacyjnego, musisz podać zgody w imieniu użytkowników, jeśli użytkownicy są ograniczeni przed wyrażanie zgody indywidualnie. W takim przypadku użytkownicy widzą komunikat "Nie masz dostępu do tej aplikacji":

![Komunikat "Nie masz dostępu do tej aplikacji", korzystając z konta służbowego lub szkolnego](media/accounts/consent-permissions-denied.png)

Aby wyrazić zgodę jako administrator, użyj [strony zgody administratora](https://notebooks.azure.com/account/adminConsent), która przeprowadzi Cię przez proces.

## <a name="next-steps"></a>Następne kroki  

> [!div class="nextstepaction"]
> [Edytuj swój profil i identyfikator użytkownika](azure-notebooks-user-profile.md)
