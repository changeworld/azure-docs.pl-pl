---
title: Konfigurowanie potoku ciągłej integracji/ciągłego wdrażania przy użyciu zadania kompilacji emulatora usługi Azure Cosmos DB
description: Samouczek dotyczący sposobu konfigurowania przepływu pracy kompilacji i wydania w usłudze Visual Studio Team Services (VSTS) przy użyciu zadania kompilacji emulatora usługi Cosmos DB
services: cosmos-db
keywords: Emulator usługi Azure Cosmos DB
author: deborahc
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: tutorial
ms.date: 8/28/2018
ms.author: dech
ms.openlocfilehash: 37bb43435c34f14145b3642aa12c5cb0f16d780c
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2018
ms.locfileid: "43783878"
---
# <a name="set-up-a-cicd-pipeline-with-the-azure-cosmos-db-emulator-build-task-in-visual-studio-team-services"></a>Konfigurowanie potoku ciągłej integracji/ciągłego wdrażania przy użyciu zadania kompilacji emulatora usługi Azure Cosmos DB w usłudze Visual Studio Team Services

Emulator usługi Azure Cosmos DB zapewnia środowisko lokalne, które emuluje usługę Azure Cosmos DB do celów programistycznych. Emulator umożliwia lokalne opracowywanie i testowanie aplikacji bez konieczności tworzenia subskrypcji platformy Azure i ponoszenia kosztów. 

Zadanie kompilacji emulatora usługi Azure Cosmos DB dla usługi Visual Studio Team Services umożliwia wykonanie tych czynności w środowisku ciągłej integracji. Dzięki zadaniu kompilacji możesz uruchamiać testy z użyciem emulatora w ramach przepływów pracy kompilacji i wydania. Zadanie uruchamia kontener platformy Docker z uruchomionym emulatorem i dostarcza punkt końcowy, który może być używany przez pozostałą część definicji kompilacji. Możesz utworzyć i uruchomić dowolną liczbę wystąpień emulatora — każde w oddzielnym kontenerze. 

W tym artykule pokazano, jak skonfigurować potok ciągłej integracji w usłudze VSTS dla aplikacji ASP.NET, w której wykorzystano zadanie kompilacji emulatora usługi Cosmos DB w celu wykonywania testów. 

## <a name="install-the-emulator-build-task"></a>Instalowanie zadania kompilacji emulatora

Aby użyć zadania kompilacji, najpierw musisz je zainstalować w swojej organizacji usługi VSTS. Znajdź rozszerzenie **Azure Cosmos DB Emulator** w witrynie [Marketplace](https://marketplace.visualstudio.com/items?itemName=azure-cosmosdb.emulator-public-preview) i kliknij polecenie **Get it free** (Uzyskaj bezpłatnie).

![Znajdowanie i instalowanie zadania kompilacji emulatora usługi Azure Cosmos DB w witrynie Marketplace usługi VSTS](./media/tutorial-setup-ci-cd/addExtension_1.png)

Następnie wybierz organizację, w której chcesz zainstalować rozszerzenie. 

> [!NOTE]
> Aby zainstalować rozszerzenie w organizacji usługi VSTS, musisz być właścicielem konta lub administratorem kolekcji projektów. Jeśli nie masz uprawnień, ale jesteś użytkownikiem konta, możesz w takiej sytuacji zażądać rozszerzenia. [Dowiedz się więcej.](https://docs.microsoft.com/vsts/marketplace/faq-extensions?view=vsts#install-request-assign-and-access-extensions) 

![Wybieranie organizacji usługi VSTS, w której chcesz zainstalować rozszerzenie](./media/tutorial-setup-ci-cd/addExtension_2.png)

## <a name="create-a-build-definition"></a>Tworzenie definicji kompilacji

Po zainstalowaniu rozszerzenia należy dodać je do [definicji kompilacji](https://docs.microsoft.com/vsts/pipelines/get-started-designer?view=vsts&tabs=new-nav). Możesz zmodyfikować istniejącą definicję kompilacji lub utworzyć nową. Jeśli masz już istniejącą definicję kompilacji, możesz przejść od razu do sekcji [Dodawanie zadania kompilacji emulatora do definicji kompilacji](#addEmulatorBuildTaskToBuildDefinition).

Aby utworzyć nową definicję kompilacji, przejdź do karty **Build and Release** (Kompilacja i wydanie) w usłudze VSTS. Wybierz pozycję **+New** (+Nowe).

![Tworzenie nowej definicji kompilacji](./media/tutorial-setup-ci-cd/CreateNewBuildDef_1.png) Wybierz odpowiedni projekt zespołowy, repozytorium i gałąź, aby włączyć kompilacje. 

![Wybieranie projektu zespołowego, repozytorium i gałęzi dla definicji kompilacji ](./media/tutorial-setup-ci-cd/CreateNewBuildDef_2.png)

Na koniec wybierz odpowiedni szablon dla definicji kompilacji. Do celów tego samouczka wybierzemy szablon **ASP.NET**. 

![Wybieranie odpowiedniego szablonu definicji kompilacji ](./media/tutorial-setup-ci-cd/CreateNewBuildDef_3.png)

Mamy już definicję kompilacji, którą możemy skonfigurować, aby użyć zadania kompilacji emulatora usługi Azure Cosmos DB, które wygląda tak jak to poniżej. 

![Szablon definicji kompilacji ASP.NET](./media/tutorial-setup-ci-cd/CreateNewBuildDef_4.png)

## <a name="addEmulatorBuildTaskToBuildDefinition"></a>Dodawanie zadania do definicji kompilacji

Aby dodać zadanie kompilacji emulatora, wyszukaj ciąg **cosmos** w polu wyszukiwania i wybierz pozycję **Add**  (Dodaj). Zadanie kompilacji uruchomi kontener z uruchomionym wystąpieniem emulatora usługi Cosmos DB, a więc to zadanie musi zostać umieszczone przed innymi zadaniami, które wymagają działającego kontenera.

![Dodawanie zadania kompilacji emulatora do definicji kompilacji](./media/tutorial-setup-ci-cd/addExtension_3.png) W tym samouczku dodamy zadanie na początku fazy 1, aby mieć pewność, że emulator będzie dostępny przed wykonaniem testów.
Ukończona definicja kompilacji wygląda teraz w następujący sposób. 

![Szablon definicji kompilacji ASP.NET](./media/tutorial-setup-ci-cd/CreateNewBuildDef_5.png)

## <a name="configure-tests-to-use-the-emulator"></a>Konfigurowanie testów w celu używania emulatora
Teraz skonfigurujemy nasze testy tak, aby używały emulatora. Zadanie kompilacji emulatora eksportuje zmienną środowiskową „CosmosDbEmulator.Endpoint”, wobec której wszystkie zadania w dalszej części potoku kompilacji mogą wysyłać żądania. 

W tym samouczku użyjemy zadania [Visual Studio Test](https://github.com/Microsoft/vsts-tasks/blob/master/Tasks/VsTestV2/README.md), aby uruchomić testy jednostkowe skonfigurowane za pomocą pliku **.runsettings**. Aby dowiedzieć się więcej na temat konfigurowania testów jednostkowych, zapoznaj się z [dokumentacją](https://docs.microsoft.com/visualstudio/test/configure-unit-tests-by-using-a-dot-runsettings-file?view=vs-2017).

Poniżej przedstawiono przykładowy plik **.runsettings**, który definiuje parametry przekazywane do testów jednostkowych aplikacji. Należy pamiętać, że używana zmienna `authKey` to [dobrze znany klucz](https://docs.microsoft.com/azure/cosmos-db/local-emulator#authenticating-requests) emulatora. Ta zmienna `authKey` to klucz oczekiwany przez zadanie kompilacji emulatora i powinna być zdefiniowana w pliku **.runsettings**.

```csharp
<RunSettings>
    <TestRunParameters>
    <Parameter name="endpoint" value="https://localhost:8081" />
    <Parameter name="authKey" value="C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==" />
    <Parameter name="database" value="ToDoListTest" />
    <Parameter name="collection" value="ItemsTest" />
  </TestRunParameters>
</RunSettings>
```
Te parametry `TestRunParameters` są wywoływane za pośrednictwem właściwości `TestContext` w projekcie testowym aplikacji. Oto przykład testu wykonywanego względem usługi Cosmos DB. 

```csharp
namespace todo.Tests
{
    [TestClass]
    public class TodoUnitTests
    {
        public TestContext TestContext { get; set; }

        [TestInitialize()]
        public void Initialize()
        {
            string endpoint = TestContext.Properties["endpoint"].ToString();
            string authKey = TestContext.Properties["authKey"].ToString();
            Console.WriteLine("Using endpoint: ", endpoint);
            DocumentDBRepository<Item>.Initialize(endpoint, authKey);
        }
        [TestMethod]
        public async Task TestCreateItemsAsync()
        {
            var item = new Item
            {
                Id = "1",
                Name = "testName",
                Description = "testDescription",
                Completed = false,
                Category = "testCategory"
            };

            // Create the item
            await DocumentDBRepository<Item>.CreateItemAsync(item);
            // Query for the item
            var returnedItem = await DocumentDBRepository<Item>.GetItemAsync(item.Id, item.Category);
            // Verify the item returned is correct.
            Assert.AreEqual(item.Id, returnedItem.Id);
            Assert.AreEqual(item.Category, returnedItem.Category);
        }

        [TestCleanup()]
        public void Cleanup()
        {
            DocumentDBRepository<Item>.Teardown();
        }
    }
}
```

Przejdź do opcji wykonywania w zadaniu Visual Studio Test. W opcji **Settings file** (Plik ustawień) określ, czy testy zostały skonfigurowane przy użyciu pliku **.runsettings**. W opcji **Override test run parameters** (Zastąp parametry przebiegu testu) dodaj element ` -endpoint $(CosmosDbEmulator.Endpoint)`. W ten sposób skonfigurujesz zadanie Test tak, aby odwoływało się do punktu końcowego zadania kompilacji emulatora, a nie do punktu końcowego zdefiniowanego w pliku **.runsettings**.  

![Zastępowanie zmiennej punktu końcowego punktem końcowym zadania kompilacji emulatora](./media/tutorial-setup-ci-cd/addExtension_5.png)

## <a name="run-the-build"></a>Uruchamianie kompilacji
Teraz zapisz kompilację i dodaj ją do kolejki. 

![Zapisywanie i uruchamianie kompilacji](./media/tutorial-setup-ci-cd/runBuild_1.png)

Po uruchomieniu kompilacji zwróć uwagę, że zadanie emulatora usługi Cosmos DB rozpoczęło ściąganie obrazu platformy Docker z zainstalowanym emulatorem. 

![Zapisywanie i uruchamianie kompilacji](./media/tutorial-setup-ci-cd/runBuild_4.png)

Po ukończeniu kompilacji zwróć uwagę, że testy uruchomione względem emulatora usługi Cosmos DB za pomocą zadania kompilacji zakończyły się powodzeniem.

![Zapisywanie i uruchamianie kompilacji](./media/tutorial-setup-ci-cd/buildComplete_1.png)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat korzystania z emulatora do celów lokalnego programowania i testowania, zobacz [Programowanie i testowanie w środowisku lokalnym przy użyciu emulatora usługi Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/local-emulator).

Aby wyeksportować certyfikaty SSL emulatora, zobacz [Eksportowanie certyfikatów emulatora usługi Azure Cosmos DB do użycia w językach Java, Python i Node.js](https://docs.microsoft.com/azure/cosmos-db/local-emulator-export-ssl-certificates)
