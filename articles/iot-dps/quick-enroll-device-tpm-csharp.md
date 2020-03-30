---
title: 'Rejestrowanie urządzenia modułu TPM w usłudze inicjowania obsługi administracyjnej urządzeń platformy Azure przy użyciu języka C #'
description: Szybki start — rejestrowanie urządzenia TPM w usłudze inicjowania obsługi administracyjnej usługi usługi Azure IoT Hub (DPS) przy użyciu zestawu SDK usługi C#. W tym przewodniku Szybki start używane są rejestracje indywidualne.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: ee1b803459e0c81b86021b617a29e0b29ee19909
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "74976846"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-c-service-sdk"></a>Szybki start: rejestrowanie urządzenia TPM w usłudze inicjowania obsługi administracyjnej urządzeń usługi IoT Hub przy użyciu sdk usługi C#

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

W tym artykule pokazano, jak programowo utworzyć indywidualną rejestrację dla urządzenia TPM w usłudze inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub przy użyciu [zestawu SDK usługi C#](https://github.com/Azure/azure-iot-sdk-csharp) i przykładowej aplikacji C# .NET Core. Opcjonalnie można zarejestrować symulowane urządzenie modułu TPM do usługi inicjowania obsługi administracyjnej przy użyciu tego indywidualnego wpisu rejestracji. Mimo że te kroki działają zarówno na komputerach z systemem Windows, jak i Linux, w tym artykule użyto komputera dewelopera systemu Windows.

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

1. Sprawdź, czy na komputerze jest zainstalowany [program Visual Studio 2019.](https://www.visualstudio.com/vs/)

1. Sprawdź, czy na komputerze jest zainstalowany [pakiet .NET Core SDK.](https://www.microsoft.com/net/download/windows)

1. Wykonaj kroki opisane w [konfigurowaniu usługi inicjowania obsługi administracyjnej urządzeń usługi Usługi obsługi urządzeń usługi IoT Hub za pomocą portalu Azure portal](./quick-setup-auto-provision.md) przed kontynuowaniem.

1. (Opcjonalnie) Jeśli chcesz zarejestrować symulowane urządzenie na końcu tego przewodnika Szybki start, wykonaj procedurę w [programie Utwórz i aprowizuj symulowane urządzenie TPM przy użyciu sDK urządzenia C#](quick-create-simulated-device-tpm-csharp.md) do kroku, w którym otrzymasz klucz poręczenia dla urządzenia. Zapisz klucz poręczenia, identyfikator rejestracji i opcjonalnie identyfikator urządzenia, ponieważ należy ich użyć w dalszej części tego przewodnika Szybki start.

   > [!NOTE]
   > Nie postępuj zgodnie z instrukcjami, aby utworzyć rejestrację indywidualną przy użyciu witryny Azure portal.

## <a name="get-the-connection-string-for-your-provisioning-service"></a>Uzyskiwanie parametrów połączenia usługi aprowizacji

Na potrzeby przykładu w tym przewodniku Szybki start będą potrzebne parametry połączenia usługi aprowizacji.

1. Zaloguj się do witryny Azure portal, wybierz **pozycję Wszystkie zasoby**, a następnie usługę inicjowania obsługi urządzeń.

1. Wybierz **pozycję Zasady dostępu współdzielonego**, a następnie wybierz zasadę dostępu, której chcesz użyć do otwarcia jej właściwości. W **programie Access Policy**skopiuj i zapisz ciąg połączenia klucza podstawowego.

    ![Pobieranie parametrów połączenia usługi aprowizacji z portalu](media/quick-enroll-device-tpm-csharp/get-service-connection-string-vs2019.png)

## <a name="create-the-individual-enrollment-sample"></a>Tworzenie przykładowej rejestracji indywidualnej

W tej sekcji pokazano, jak utworzyć aplikację konsoli .NET Core, która dodaje indywidualną rejestrację dla urządzenia TPM do usługi inicjowania obsługi administracyjnej. Wprowadzając pewne modyfikacje, za pomocą tych kroków można również utworzyć aplikację konsolową [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot) służącą do dodawania rejestracji indywidualnej. Aby dowiedzieć się więcej o opracowywaniu za pomocą programu IoT Core, zobacz [Dokumentacja dewelopera programu Windows IoT Core](https://docs.microsoft.com/windows/iot-core/).

1. Otwórz program Visual Studio i wybierz pozycję **Utwórz nowy projekt**. W **obszarze Tworzenie nowego projektu**wybierz szablon projektu Aplikacji konsoli **(.NET Core)** dla języka C# i wybierz pozycję **Dalej**.

1. Nazwij projekt *CreateTpmEnrollment*i naciśnij klawisz **Utwórz**.

    ![Konfigurowanie projektu pulpitu klasycznego systemu Windows w języku Visual C#](media/quick-enroll-device-tpm-csharp/configure-tpm-app-vs2019.png)

1. Po otwarciu rozwiązania w programie Visual Studio w okienku **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt **CreateTpmEnrollment.** Wybierz pozycję **Zarządzaj pakietami NuGet**.

1. W **Menedżerze pakietów NuGet**wybierz pozycję **Przeglądaj**, wyszukaj i wybierz pozycję **Microsoft.Azure.Devices.Provisioning.Service**, a następnie naciśnij **pozycję Zainstaluj**.

   ![Okno Menedżera pakietów NuGet](media//quick-enroll-device-tpm-csharp/add-nuget.png)

   Ten krok pobiera, instaluje i dodaje odwołanie do pakietu [NuGet klienta usługi inicjowania obsługi administracyjnej platformy Azure](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) i jego zależności.

1. Dodaj następujące `using` instrukcje `using` po innych instrukcjach w górnej części: `Program.cs`
  
   ```csharp
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Provisioning.Service;
   ```

1. Dodaj następujące pola `Program` do klasy, wykonując zmiany wymienione poniżej.

   ```csharp
   private static string ProvisioningConnectionString = "{ProvisioningServiceConnectionString}";
   private const string RegistrationId = "sample-registrationid-csharp";
   private const string TpmEndorsementKey =
       "AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUS" +
       "cTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3d" +
       "yKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKR" +
       "dln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFe" +
       "WlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==";
       
   // Optional parameters
   private const string OptionalDeviceId = "myCSharpDevice";
   private const ProvisioningStatus OptionalProvisioningStatus = ProvisioningStatus.Enabled;
   ```

   * Zastąp wartość symbolu `ProvisioningServiceConnectionString` zastępczego ciągiem połączenia usługi inicjowania obsługi administracyjnej, dla której chcesz utworzyć rejestrację.

   * Opcjonalnie możesz zmienić identyfikator rejestracji, klucz poręczenia, identyfikator urządzenia i stan aprowizowania.

   * Jeśli używasz tego przewodnika Szybki start wraz z [tworzeniem i inicjowania obsługi administracyjnej symulowanego urządzenia TPM przy użyciu modułu SDK urządzenia C#](quick-create-simulated-device-tpm-csharp.md) w celu zainicjowania obsługi symulowanego urządzenia, zastąp klucz poręczenia i identyfikator rejestracji wartościami odnotowanymi w tym przewodniku Szybki start. Identyfikator urządzenia można zastąpić wartością sugerowaną w tym przewodniku Szybki start, użyć własnej wartości lub użyć wartości domyślnej w tym przykładzie.

1. Dodaj następującą metodę `Program` do klasy.  Ten kod tworzy indywidualny wpis `CreateOrUpdateIndividualEnrollmentAsync` rejestracji, `ProvisioningServiceClient` a następnie wywołuje metodę, aby dodać indywidualną rejestrację do usługi inicjowania obsługi administracyjnej.

   ```csharp
   public static async Task RunSample()
   {
       Console.WriteLine("Starting sample...");

       using (ProvisioningServiceClient provisioningServiceClient =
               ProvisioningServiceClient.CreateFromConnectionString(ProvisioningConnectionString))
       {
           #region Create a new individualEnrollment config
           Console.WriteLine("\nCreating a new individualEnrollment...");
           Attestation attestation = new TpmAttestation(TpmEndorsementKey);
           IndividualEnrollment individualEnrollment =
                   new IndividualEnrollment(
                           RegistrationId,
                           attestation);

           // The following parameters are optional. Remove them if you don't need them.
           individualEnrollment.DeviceId = OptionalDeviceId;
           individualEnrollment.ProvisioningStatus = OptionalProvisioningStatus;
           #endregion

           #region Create the individualEnrollment
           Console.WriteLine("\nAdding new individualEnrollment...");
           IndividualEnrollment individualEnrollmentResult =
               await provisioningServiceClient.CreateOrUpdateIndividualEnrollmentAsync(individualEnrollment).ConfigureAwait(false);
           Console.WriteLine("\nIndividualEnrollment created with success.");
           Console.WriteLine(individualEnrollmentResult);
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

## <a name="run-the-individual-enrollment-sample"></a>Uruchamianie przykładowej rejestracji indywidualnej
  
Uruchom przykład w programie Visual Studio, aby utworzyć rejestrację indywidualną dla urządzenia TPM.

Pojawi się okno wiersza polecenia i rozpocznie się wyświetlanie komunikatów potwierdzających. Po pomyślnym utworzeniu okno wiersza polecenia wyświetla właściwości nowej rejestracji indywidualnej.

Można sprawdzić, czy rejestracja indywidualna została utworzona. Przejdź do podsumowania usługi inicjowania obsługi administracyjnej urządzeń i wybierz pozycję **Zarządzaj rejestracjami,** a następnie wybierz pozycję **Rejestracje indywidualne**. Powinien zostać wyświetlony nowy wpis rejestracji odpowiadający identyfikatorowi rejestracji użytemu w przykładzie.

![Właściwości rejestracji w portalu](media/quick-enroll-device-tpm-csharp/verify-enrollment-portal-vs2019.png)

Wybierz wpis, aby zweryfikować klucz poręczenia i inne właściwości zapisu.

Jeśli wykonując kroki opisane w [programie Utwórz i aprowizować symulowane urządzenie TPM przy użyciu paska Szybki start urządzenia C#,](quick-create-simulated-device-tpm-csharp.md) możesz kontynuować pozostałe kroki w tym przewodniku Szybki start, aby zarejestrować symulowane urządzenie. Pomiń kroki tworzenia rejestracji indywidualnej przy użyciu witryny Azure Portal.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz eksplorować przykład usługi Języka C#, nie czyścić zasobów utworzonych w tym przewodniku Szybki start. W przeciwnym razie należy wykonać następujące kroki, aby usunąć wszystkie zasoby utworzone przez ten przewodnik Szybki start.

1. Zamknij okno wyjściowe przykładu języka C# na komputerze.

1. Przejdź do usługi inicjowania obsługi urządzeń w portalu Azure, wybierz pozycję **Zarządzaj rejestracjami**, a następnie wybierz kartę **Rejestracje indywidualne.** Zaznacz pole wyboru obok *identyfikatora rejestracji* dla wpisu rejestracji utworzonego przy użyciu tego przewodnika Szybki start, a następnie naciśnij przycisk **Usuń** u góry okienka.

1. Jeśli wykonano kroki opisane w [tworzenie i aprowiz wiązki obsługi administracyjnej urządzenia TPM przy użyciu sdk urządzenia C#](quick-create-simulated-device-tpm-csharp.md) w celu utworzenia symulowanego urządzenia modułu TPM, wykonaj następujące czynności:

    1. Zamknij okno symulatora modułu TPM i przykładowe okno danych wyjściowych urządzenia symulowanego.

    1. W witrynie Azure Portal przejdź do usługi IoT Hub, w której zaaprowizowano urządzenie. W menu **w obszarze Eksploratorzy**wybierz pozycję **Urządzenia IoT**zaznacz pole wyboru obok *identyfikatora URZĄDZENIA* urządzenia zarejestrowanego w tym przewodniku Szybki start, a następnie naciśnij przycisk **Usuń** u góry okienka.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start programowo utworzono indywidualny wpis rejestracji dla urządzenia TPM. Opcjonalnie utworzono symulowane urządzenie modułu TPM na komputerze i zainicjowano go do centrum IoT hub przy użyciu usługi inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub. Aby uzyskać dokładne informacje na temat aprowizowania urządzeń, przejdź do samouczka poświęconego konfiguracji usługi Device Provisioning Service w witrynie Azure portal.

> [!div class="nextstepaction"]
> [Samouczki dla usługi Azure IoT Hub Device Provisioning Service](./tutorial-set-up-cloud.md)
