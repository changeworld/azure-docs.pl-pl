---
title: 'Rejestrowanie urządzenia X.509 w usłudze inicjowania obsługi urządzeń platformy Azure przy użyciu języka C #'
description: W tym przewodniku Szybki start używane są rejestracje grupowe. W tym przewodniku Szybki start należy zarejestrować urządzenia X.509 w usłudze inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub (DPS) przy użyciu języka C#.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: 64bc3921a606ab3211173b46b268ded53952c8bb
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75434659"
---
# <a name="quickstart-enroll-x509-devices-to-the-device-provisioning-service-using-c"></a>Przewodnik Szybki start: rejestrowanie urządzeń X.509 w usłudze Device Provisioning Service przy użyciu języka C#

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

Ten przewodnik szybki Start przedstawia sposób użycia języka C# w celu programowego utworzenia [grupy rejestracji](concepts-service.md#enrollment-group), która będzie używać certyfikatów X.509 pośredniego lub głównego urzędu certyfikacji. Grupa rejestracji jest tworzona przy użyciu [zestawu SDK IoT platformy Microsoft Azure dla platformy .NET](https://github.com/Azure/azure-iot-sdk-csharp) i przykładowej aplikacji C# .NET Core. Grupa rejestracji steruje dostępem do usługi aprowizacji dla urządzeń, które mają wspólny certyfikat podpisywania w swoim łańcuchu certyfikatów. Aby dowiedzieć się więcej, zobacz [Sterowanie dostępem urządzenia do usługi aprowizacji za pomocą certyfikatów X.509](./concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates). Aby uzyskać więcej informacji na temat używania infrastruktury kluczy publicznych opartej na certyfikatach X.509 z usługą Azure IoT Hub i Device Provisioning, zobacz [Omówienie zabezpieczeń certyfikatu X.509 urzędu certyfikacji](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview). 

Ten przewodnik Szybki start oczekuje, że utworzono już centrum IoT hub i wystąpienie usługi inicjowania obsługi administracyjnej urządzeń. Jeśli te zasoby nie zostały jeszcze utworzone, przed kontynuowaniem tego artykułu należy [ukończyć usługę inicjowania obsługi administracyjnej usługi inicjowania obsługi administracyjnej usługi usługi inicjowania obsługi administracyjnej usługi usługi Azure portal](./quick-setup-auto-provision.md) portal.

Chociaż kroki opisane w tym artykule działają zarówno na komputerach z systemem Windows, jak i Linux, w tym artykule użyto komputera dewelopera z systemem Windows.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj program [Visual Studio 2019](https://www.visualstudio.com/vs/).
* Zainstaluj [pakiet .NET Core SDK](https://www.microsoft.com/net/download/windows).
* Zainstaluj [Git](https://git-scm.com/download/).

## <a name="prepare-test-certificates"></a>Przygotowywanie certyfikatów testowych

Na potrzeby tego przewodnika Szybki start musisz mieć plik pem lub cer, który zawiera publiczną część certyfikatu X.509 pośredniego lub głównego urzędu certyfikacji. Ten certyfikat musi zostać przekazany do usługi aprowizacji i zweryfikowany przez usługę.

Zestaw [SDK języka Azure IoT C](https://github.com/Azure/azure-iot-sdk-c) zawiera narzędzia testowe, które mogą pomóc w utworzeniu łańcucha certyfikatów X.509, przekazaniu certyfikatu głównego lub pośredniego z tego łańcucha i wykonaj dowód posiadania usługi w celu zweryfikowania certyfikatu.

> [!CAUTION]
> Użyj certyfikatów utworzonych za pomocą narzędzi zestawu SDK tylko do testowania rozwoju.
> Nie należy używać tych certyfikatów w produkcji.
> Zawierają one hasła zakodowane na twardo, takie jak *1234*, które wygasają po 30 dniach.
> Informacje na temat uzyskiwania certyfikatów odpowiednich do użycia w produkcji znajdują się w artykule [Jak uzyskać certyfikat X.509 urzędu certyfikacji](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) w dokumentacji usługi Azure IoT Hub.
>

Aby użyć tego narzędzia testowego do generowania certyfikatów, wykonaj następujące czynności:

1. Znajdź nazwę tagu dla [najnowszej wersji](https://github.com/Azure/azure-iot-sdk-c/releases/latest) SDK C usługi Azure IoT.

2. Otwórz wiersz polecenia lub powłokę Git Bash i przejdź do folderu roboczego na swojej maszynie. Uruchom następujące polecenia, aby sklonować najnowszą wersję repozytorium GitHub [SDK usługi Azure IoT C.](https://github.com/Azure/azure-iot-sdk-c) Użyj znacznika znalezionego w poprzednim kroku `-b` jako wartości parametru:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Należy się spodziewać, że ukończenie operacji potrwa kilka minut.

   Narzędzia testowe znajdują się w folderze *azure-iot-sdk-c/tools/CACertificates* sklonowanego repozytorium.

3. Postępuj zgodnie z instrukcjami opisanymi w artykule [Zarządzanie certyfikatami testowymi urzędu certyfikacji na potrzeby przykładów i samouczków](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

Oprócz narzędzi w zestawie C SDK [przykład weryfikacji certyfikatu grupy](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample) w zestawie *Microsoft Azure IoT SDK dla platformy .NET* pokazuje, jak wykonać dowód posiadania w języku C# przy istniejącym pośrednim lub głównym certyfikacie urzędu certyfikacji X.509.

## <a name="get-the-connection-string-for-your-provisioning-service"></a>Uzyskiwanie parametrów połączenia usługi aprowizacji

Na potrzeby przykładu w tym przewodniku Szybki start będą potrzebne parametry połączenia usługi aprowizacji.

1. Zaloguj się do witryny Azure portal, wybierz **pozycję Wszystkie zasoby**, a następnie usługę inicjowania obsługi urządzeń.

1. Wybierz **pozycję Zasady dostępu współdzielonego**, a następnie wybierz zasadę dostępu, której chcesz użyć do otwarcia jej właściwości. W **programie Access Policy**skopiuj i zapisz ciąg połączenia klucza podstawowego.

    ![Pobieranie parametrów połączenia usługi aprowizacji z portalu](media/quick-enroll-device-x509-csharp/get-service-connection-string-vs2019.png)

## <a name="create-the-enrollment-group-sample"></a>Tworzenie przykładowej grupy rejestracji 

W tej sekcji pokazano, jak utworzyć aplikację konsoli .NET Core, która dodaje grupę rejestracji do usługi inicjowania obsługi administracyjnej. Wprowadzając pewne modyfikacje, za pomocą tych kroków można również utworzyć aplikację konsolową [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot) służącą do dodawania grupy rejestracji. Aby dowiedzieć się więcej na temat programowania w systemie IoT Core, zobacz [dokumentację dla deweloperów systemu Windows IoT Core](https://docs.microsoft.com/windows/iot-core/).

1. Otwórz program Visual Studio i wybierz pozycję **Utwórz nowy projekt**. W **obszarze Tworzenie nowego projektu**wybierz szablon projektu Aplikacji konsoli **(.NET Core)** dla języka C# i wybierz pozycję **Dalej**.

1. Nazwij projekt *CreateEnrollmentGroup*, a następnie naciśnij klawisz **Create**.

    ![Konfigurowanie projektu pulpitu klasycznego systemu Windows w języku Visual C#](media//quick-enroll-device-x509-csharp/configure-app-vs2019.png)

1. Po otwarciu rozwiązania w programie Visual Studio w okienku **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt **CreateEnrollmentGroup,** a następnie wybierz pozycję **Zarządzaj pakietami NuGet**.

1. W **Menedżerze pakietów NuGet**wybierz pozycję **Przeglądaj**, wyszukaj i wybierz pozycję **Microsoft.Azure.Devices.Provisioning.Service**, a następnie naciśnij **pozycję Zainstaluj**.

    ![Okno Menedżera pakietów NuGet](media//quick-enroll-device-x509-csharp/add-nuget.png)

   Ten krok pobiera, instaluje i dodaje odwołanie do pakietu [NuGet klienta usługi inicjowania obsługi administracyjnej platformy Azure](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) i jego zależności.

1. Dodaj następujące `using` instrukcje `using` po innych instrukcjach w górnej części: `Program.cs`

   ```csharp
   using System.Security.Cryptography.X509Certificates;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Provisioning.Service;
   ```

1. Dodaj następujące pola `Program` do klasy i wykonuj wymienione zmiany.  

   ```csharp
   private static string ProvisioningConnectionString = "{ProvisioningServiceConnectionString}";
   private static string EnrollmentGroupId = "enrollmentgrouptest";
   private static string X509RootCertPath = @"{Path to a .cer or .pem file for a verified root CA or intermediate CA X.509 certificate}";
   ```

   * Zastąp wartość symbolu `ProvisioningServiceConnectionString` zastępczego ciągiem połączenia usługi inicjowania obsługi administracyjnej, dla której chcesz utworzyć rejestrację.

   * Zastąp wartość symbolu `X509RootCertPath` zastępczego ścieżką do pliku pem lub cer. Ten plik reprezentuje publiczną część pośredniego lub głównego certyfikatu URZĘDU CERTYFIKACJI X.509, który został wcześniej przekazany i zweryfikowany za pomocą usługi inicjowania obsługi administracyjnej.

   * Opcjonalnie można zmienić `EnrollmentGroupId` wartość. Ciąg może zawierać tylko małe litery i łączniki.

   > [!IMPORTANT]
   > W kodzie produkcyjnym pamiętaj o następujących kwestiach dotyczących zabezpieczeń:
   >
   > * Trwałe kodowanie parametrów połączenia administratora usługi aprowizacji jest niezgodne z najlepszymi rozwiązaniami dotyczącymi zabezpieczeń. Zamiast tego parametry połączenia powinny być przechowywane w sposób bezpieczny, np. w zabezpieczonym pliku konfiguracji lub rejestrze.
   > * Pamiętaj, aby przekazać tylko publiczną część certyfikatu podpisywania. Nigdy nie przekazuj plików pfx (PKCS12) ani plików pem zawierających klucze prywatne do usługi aprowizacji.

1. Dodaj następującą metodę `Program` do klasy. Ten kod tworzy wpis grupy rejestracji, a następnie wywołuje `CreateOrUpdateEnrollmentGroupAsync` metodę, `ProvisioningServiceClient` aby dodać grupę rejestracji do usługi inicjowania obsługi administracyjnej.

   ```csharp
   public static async Task RunSample()
   {
       Console.WriteLine("Starting sample...");
 
       using (ProvisioningServiceClient provisioningServiceClient =
               ProvisioningServiceClient.CreateFromConnectionString(ProvisioningConnectionString))
       {
           #region Create a new enrollmentGroup config
           Console.WriteLine("\nCreating a new enrollmentGroup...");
           var certificate = new X509Certificate2(X509RootCertPath);
           Attestation attestation = X509Attestation.CreateFromRootCertificates(certificate);
           EnrollmentGroup enrollmentGroup =
                   new EnrollmentGroup(
                           EnrollmentGroupId,
                           attestation)
                   {
                       ProvisioningStatus = ProvisioningStatus.Enabled
                   };
           Console.WriteLine(enrollmentGroup);
           #endregion
 
           #region Create the enrollmentGroup
           Console.WriteLine("\nAdding new enrollmentGroup...");
           EnrollmentGroup enrollmentGroupResult =
               await provisioningServiceClient.CreateOrUpdateEnrollmentGroupAsync(enrollmentGroup).ConfigureAwait(false);
           Console.WriteLine("\nEnrollmentGroup created with success.");
           Console.WriteLine(enrollmentGroupResult);
           #endregion
 
       }
   }
   ```

1. Na koniec zastąp `Main` treść metody następującymi wierszami:

   ```csharp
   RunSample().GetAwaiter().GetResult();
   Console.WriteLine("\nHit <Enter> to exit ...");
   Console.ReadLine();
   ```

1. Skompiluj rozwiązanie.

## <a name="run-the-enrollment-group-sample"></a>Uruchamianie przykładowej grupy rejestracji
  
Uruchom przykład w programie Visual Studio, aby utworzyć grupę rejestracji. Pojawi się okno wiersza polecenia i rozpocznie się wyświetlanie komunikatów potwierdzających. Po pomyślnym utworzeniu okno wiersz polecenia wyświetla właściwości nowej grupy rejestracji.

Można sprawdzić, czy grupa rejestracji została utworzona. Przejdź do podsumowania usługi inicjowania obsługi administracyjnej urządzeń i wybierz pozycję **Zarządzaj rejestracjami,** a następnie wybierz pozycję **Grupy rejestracji**. Powinien zostać wyświetlony nowy wpis rejestracji odpowiadający identyfikatorowi rejestracji użytemu w przykładzie.

![Właściwości rejestracji w portalu](media/quick-enroll-device-x509-csharp/verify-enrollment-portal-vs2019.png)

Wybierz wpis, aby zweryfikować odcisk palca certyfikatu i inne właściwości wpisu.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz eksplorować przykład usługi Języka C#, nie czyścić zasobów utworzonych w tym przewodniku Szybki start. W przeciwnym razie należy wykonać następujące kroki, aby usunąć wszystkie zasoby utworzone przez ten przewodnik Szybki start.

1. Zamknij okno wyjściowe przykładu języka C# na komputerze.

1. Przejdź do usługi inicjowania obsługi urządzeń w portalu Azure, wybierz pozycję **Zarządzaj rejestracjami,** a następnie wybierz pozycję **Grupy rejestracji**. Wybierz *identyfikator rejestracji* dla wpisu rejestracji utworzonego przy użyciu tego przewodnika Szybki start i naciśnij klawisz **Delete**.

1. W usłudze inicjowania obsługi urządzeń w witrynie Azure portal wybierz pozycję **Certyfikaty**, wybierz certyfikat przekazany dla tego przewodnika Szybki start i naciśnij klawisz **Delete** u góry **strony Szczegóły certyfikatu**.  

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono grupę rejestracji dla pośredniego lub głównego certyfikatu urzędu certyfikacji X.509 przy użyciu usługi inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub. Aby uzyskać dokładne informacje na temat aprowizowania urządzeń, przejdź do samouczka poświęconego konfiguracji usługi Device Provisioning Service w witrynie Azure portal.

> [!div class="nextstepaction"]
> [Samouczki dla usługi Azure IoT Hub Device Provisioning Service](./tutorial-set-up-cloud.md)
