---
title: Rozwiązywanie problemów - QnA Maker
description: Wyselekcjonowana lista najczęściej zadawanych pytań dotyczących usługi QnA Maker pomoże Ci szybciej i z lepszymi wynikami.
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: diberry
ms.openlocfilehash: 7847e21dbcf07f669d6802fffdd1e43623a72340
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804354"
---
# <a name="troubleshooting-for-qna-maker"></a>Rozwiązywanie problemów dla programu QnA Maker

Wyselekcjonowana lista najczęściej zadawanych pytań dotyczących usługi QnA Maker pomoże Ci szybciej i z lepszymi wynikami.

<a name="how-to-get-the-qnamaker-service-hostname"></a>

## <a name="manage-predictions"></a>Zarządzanie prognozami

<details>
<summary><b>Jak poprawić wydajność przepływności dla prognozowania zapytań?</b></summary>

**Odpowiedź:** Problemy z wydajnością przepływności wskazują, że należy skalować w górę zarówno dla usługi aplikacji, jak i wyszukiwania poznawczego. Należy rozważyć dodanie repliki do wyszukiwania poznawczego, aby zwiększyć wydajność.

Dowiedz się więcej o [warstwach cenowych](Concepts/azure-resources.md).
</details>

<details>
<summary><b>Jak uzyskać punkt końcowy usługi QnAMaker</b></summary>

**Odpowiedź**: Punkt końcowy usługi QnAMaker jest przydatny do celów debugowania podczas kontaktowania się z pomocą techniczną QnAMaker lub uservoice. Punkt końcowy jest adresem URL `https://your-resource-name.azurewebsites.net`w tym formularzu: .

1. Przejdź do usługi QnAMaker (grupy zasobów) w [witrynie Azure portal](https://portal.azure.com)

    ![Grupa zasobów platformy QnAMaker Azure w witrynie Azure portal](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Wybierz usługę aplikacji skojarzoną z zasobem QnA Maker. Zazwyczaj nazwy są takie same.

     ![Wybierz usługę aplikacji QnAMaker](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. Adres URL punktu końcowego jest dostępny w sekcji Przegląd

    ![Punkt końcowy programu QnAMaker](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)

</details>

## <a name="manage-the-knowledge-base"></a>Zarządzanie bazą wiedzy

<details>
<summary><b>Przypadkowo usunąłem część programu QnA Maker, co mam zrobić?</b></summary>

**Odpowiedź:** Nie należy usuwać żadnych usług platformy Azure utworzonych wraz z zasobem QnA Maker, takim jak wyszukiwanie lub aplikacja sieci Web. Są one niezbędne do działania programu QnA Maker, jeśli go usuniesz, QnA Maker przestanie działać poprawnie.

Wszystkie usunięcia są trwałe, w tym pary pytań i odpowiedzi, pliki, adresy URL, niestandardowe pytania i odpowiedzi, bazy wiedzy lub zasoby platformy Azure. Przed usunięciem dowolnej części bazy wiedzy należy wyeksportować bazę wiedzy ze strony **Ustawienia.**

</details>

<details>
<summary><b>Dlaczego mój adres URL/pliki nie wyodrębniają par pytań i odpowiedzi?</b></summary>

**Odpowiedź:** Możliwe, że program QnA Maker nie może automatycznie wyodrębnić niektórych treści pytań i odpowiedzi (QnA) z prawidłowych adresów URL często zadawanych pytań. W takich przypadkach można wkleić zawartość QnA w pliku txt i sprawdzić, czy narzędzie może ją pochłonieć. Alternatywnie możesz edytorze dodawać treści do swojej bazy wiedzy za pośrednictwem [portalu QnA Maker](https://qnamaker.ai).

</details>

<details>
<summary><b>Jak duża jest baza wiedzy, które mogę utworzyć?</b></summary>

**Odpowiedź:** Rozmiar bazy wiedzy zależy od jednostki SKU wyszukiwania platformy Azure, którą wybierzesz podczas tworzenia usługi QnA Maker. Przeczytaj [tutaj,](./Tutorials/choosing-capacity-qnamaker-deployment.md) aby uzyskać więcej informacji.

</details>

<details>
<summary><b>Dlaczego nie widzę niczego w rozwijanej, gdy próbuję utworzyć nową bazę wiedzy?</b></summary>

**Odpowiedź:** Nie utworzono jeszcze żadnych usług QnA Maker na platformie Azure. Przeczytaj [tutaj,](./How-To/set-up-qnamaker-service-azure.md) aby dowiedzieć się, jak to zrobić.

</details>

<details>
<summary><b>Jak udostępnić bazę wiedzy innym osobom?</b></summary>

**Odpowiedź**: Udostępnianie działa na poziomie usługi QnA Maker, czyli wszystkie bazy wiedzy w usłudze będą udostępniane. Przeczytaj [tutaj,](./How-To/collaborate-knowledge-base.md) jak współpracować nad bazą wiedzy.

</details>

<details>
<summary><b>Czy można udostępnić bazę wiedzy współautorowi, który nie znajduje się w tej samej dzierżawie usługi AAD, aby zmodyfikować bazę wiedzy?</b></summary>

**Odpowiedź:** Udostępnianie opiera się na kontroli dostępu opartej na rolach platformy Azure (RBAC). Jeśli możesz udostępnić _dowolny_ zasób na platformie Azure innemu użytkownikowi, możesz również udostępnić program QnA Maker.

</details>

<details>
<summary><b>Jeśli masz plan usługi app z 5 baz wiedzy QnAMaker. Czy można przypisać prawa do odczytu/zapisu 5 różnym użytkownikom, aby każdy z nich miał dostęp tylko do 1 bazy wiedzy QnAMaker?</b></summary>

**Odpowiedź**: Możesz udostępnić całą usługę QnAMaker, a nie indywidualne bazy wiedzy.

</details>

<details>
<summary><b>Jak mogę zmienić domyślny komunikat, gdy nie zostanie znaleziony dobry mecz?</b></summary>

**Odpowiedź**: Domyślna wiadomość jest częścią ustawień w usłudze aplikacji.
- Przejdź do zasobu usługi aplikacji w witrynie Azure portal

![usługa aplikacji qnamaker](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Kliknij opcję **Ustawienia**

![ustawienia usługi aplikacji qnamaker](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Zmienianie wartości ustawienia **DefaultAnswer**
- Ponowne uruchamianie usługi aplikacji

![Ponowne uruchomienie usługi aplikacji qnamaker](./media/qnamaker-faq/qnamaker-appservice-restart.png)


</details>

<details>
<summary><b>Dlaczego moje łącze programu SharePoint nie jest wyodrębniane?</b></summary>

**Odpowiedź**: Aby uzyskać więcej informacji, zobacz [Lokalizacje źródeł danych.](./Concepts/knowledge-base.md#data-source-locations)

</details>

<details>
<summary><b>Aktualizacje, które zrobiłem do mojej bazy wiedzy nie są odzwierciedlane na publikowanie. Dlaczego nie?</b></summary>

**Odpowiedź**: Każda operacja edycji, czy to w aktualizacji tabeli, test lub ustawienie, musi być zapisany, zanim będzie można opublikować. Pamiętaj, aby kliknąć przycisk **Zapisz i trenuj** po każdej operacji edycji.

</details>

<details>
<summary><b>Czy baza wiedzy obsługuje bogate dane lub multimedia?</b></summary>

**Odpowiedź**:

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>Automatyczne wyodrębnianie multimediów plików i adresów URL

* ADRESY URL — ograniczona funkcja konwersji HTML do markdown.
* Pliki — nie są obsługiwane

#### <a name="answer-text-in-markdown"></a>Odpowiedz na tekst w znacznikach
Gdy pary QnA znajdują się w bazie wiedzy, można edytować tekst znaczników odpowiedzi, aby uwzględnić łącza do multimediów dostępnych z publicznych adresów URL.


</details>

<details>
<summary><b>Czy QnA Maker obsługuje języki inne niż angielskie?</b></summary>

**Odpowiedź**: Zobacz więcej szczegółów na temat [obsługiwanych języków](./Overview/languages-supported.md).

Jeśli masz zawartość z wielu języków, należy utworzyć oddzielną usługę dla każdego języka.

</details>

## <a name="manage-service"></a>Zarządzanie usługą

<details>
<summary><b>Kiedy należy ponownie uruchomić usługę aplikacji?</b></summary>

**Odpowiedź**: Odśwież usługę aplikacji, gdy ikona ostrzeżenia znajduje się obok wartości wersji bazy wiedzy w tabeli **Klucze punktu końcowego** na [stronie](https://www.qnamaker.ai/UserSettings) **Ustawienia użytkownika** .

</details>

<details>
<summary><b>Usunąłem istniejącą usługę wyszukiwania. Jak mogę to naprawić?</b></summary>

**Odpowiedź:** Jeśli usuniesz indeks usługi Azure Cognitive Search, operacja jest ostateczna i indeksu nie można odzyskać.

</details>

<details>
<summary><b>Usunąłem `testkb` indeks w usłudze wyszukiwania. Jak mogę to naprawić?</b></summary>

**Odpowiedź**: Starych danych nie można odzyskać. Utwórz nowy zasób QnA Maker i ponownie stwórz swoją bazę wiedzy.

</details>

<details>
<summary><b>Kiedy należy odświeżyć klucze punktu końcowego?</b></summary>

**Odpowiedź:** Odśwież klucze punktu końcowego, jeśli podejrzewasz, że zostały naruszone.

</details>

<details>
<summary><b>Czy mogę używać tego samego zasobu usługi Azure Cognitive Search dla baz wiedzy przy użyciu wielu języków?</b></summary>

**Odpowiedź:** Aby używać wielu języków i wielu baz wiedzy, użytkownik musi utworzyć zasób QnA Maker dla każdego języka. Spowoduje to utworzenie oddzielnej usługi wyszukiwania platformy Azure na język. Mieszanie różnych baz wiedzy językowej w jednej usłudze wyszukiwania platformy Azure spowoduje pogorszenie trafności wyników.

</details>

<details>
<summary><b>Jak zmienić nazwę zasobu usługi Azure Cognitive Search używanego przez program QnA Maker?</b></summary>

**Odpowiedź:** Nazwa zasobu usługi Azure Cognitive Search to nazwa zasobu programu QnA Maker z kilkoma losowymi literami dołączanymi na końcu. Utrudnia to rozróżnienie między wieloma zasobami wyszukiwania dla programu QnA Maker. Utwórz oddzielną usługę wyszukiwania (nazywając ją tak, jak chcesz) i połącz ją z usługą QnA. Kroki są podobne do kroków, które należy wykonać, aby [uaktualnić wyszukiwanie na platformie Azure](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service).

</details>

<details>
<summary><b>Kiedy QnA `Runtime core is not initialized,` Maker zwraca jak to naprawić?</b></summary>

**Odpowiedź**: Miejsce na dysku dla usługi aplikacji może być zapełniony. Czynności mające na celu naprawienie miejsca na dysku:

1. W [witrynie Azure portal](https://portal.azure.com)wybierz usługę aplikacji QnA Maker, a następnie zatrzymaj usługę.
1. Podczas gdy nadal w usłudze aplikacji, wybierz narzędzia **programistyczne**, a następnie **zaawansowane narzędzia**, a następnie **Przejdź**. Spowoduje to otwarcie nowego okna przeglądarki.
1. Wybierz **konsolę debugowania**, a następnie **cmd,** aby otworzyć narzędzie wiersza polecenia.
1. Przejdź do _katalogu site/wwwroot/Data/QnAMaker/._
1. Usuń wszystkie foldery, których `rd`nazwa zaczyna się od .

    **Nie należy usuwać** następujących elementów:

    * Plik KbIdToRankerMappings.txt
    * Plik EndpointSettings.json
    * Folder Klawisze endpointkeys

1. Uruchom usługę aplikacji.
1. Uzyskaj dostęp do bazy wiedzy, aby sprawdzić, czy działa teraz.

</details>

## <a name="integrate-with-other-services-including-bots"></a>Integracja z innymi usługami, w tym botami

<details>
<summary><b>Czy muszę używać programu Bot Framework, aby korzystać z programu QnA Maker?</b></summary>

**Odpowiedź:** Nie, nie musisz używać [programu Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) z programem QnA Maker. Jednak QnA Maker jest oferowany jako jeden z kilku szablonów w [usłudze Azure Bot Service.](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) Usługa Bot Service umożliwia szybkie inteligentne tworzenie botów za pośrednictwem programu Microsoft Bot Framework i działa w środowisku bez serwera.

</details>

<details>
<summary><b>Jak utworzyć nowego bota za pomocą programu QnA Maker?</b></summary>

**Odpowiedź:** Postępuj zgodnie z instrukcjami w [tej](./Quickstarts/create-publish-knowledge-base.md) dokumentacji, aby utworzyć bota za pomocą usługi Azure Bot Service.

</details>

<details>
<summary><b>Jak korzystać z innej bazy wiedzy z istniejącą usługą botów platformy Azure?</b></summary>

**Odpowiedź**: Musisz mieć następujące informacje o swojej bazie wiedzy:

* Identyfikator bazy wiedzy.
* Opublikowana nazwa poddomeny opublikowanego punktu końcowego bazy wiedzy, znana jako `host`, znaleziona na stronie **Ustawienia** po opublikowaniu.
* Opublikowany klucz punktu końcowego bazy wiedzy — znaleziony na stronie **Ustawienia** po opublikowaniu.

Dzięki tym informacjom przejdź do usługi aplikacji bota w witrynie Azure portal. W **obszarze Ustawienia -> Konfiguracja -> ustawienia aplikacji**zmień te wartości.

Klucz punktu końcowego bazy wiedzy `QnAAuthkey` jest oznaczony w usłudze ABS.

</details>

<details>
<summary><b>Czy dwie lub więcej aplikacji klienckich może współużytkować bazę wiedzy?</b></summary>

**Odpowiedź**: Tak, baza wiedzy może być wyszukiwana z dowolnej liczby klientów. Jeśli odpowiedź z bazy wiedzy wydaje się być powolny lub limit czasu, należy rozważyć uaktualnienie warstwy usług dla usługi aplikacji skojarzone z bazą wiedzy.

</details>

<details>
<summary><b>Jak osadzić usługę QnA Maker w mojej witrynie?</b></summary>

**Odpowiedź**: Wykonaj następujące kroki, aby osadzić usługę QnA Maker jako formę czatu internetowego w witrynie:

1. Utwórz bota z często zadawanymi pytaniami, postępując zgodnie z instrukcjami [tutaj](./Quickstarts/create-publish-knowledge-base.md).
2. Włącz czat internetowy, wykonując kroki [tutaj](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat)

</details>

## <a name="data-storage"></a>Magazyn danych

<details>
<summary><b>Jakie dane są przechowywane i gdzie są przechowywane?</b></summary>

**Odpowiedź**:

Podczas tworzenia usługi QnA Maker wybrano region platformy Azure. Bazy wiedzy i pliki dziennika są przechowywane w tym regionie.

</details>