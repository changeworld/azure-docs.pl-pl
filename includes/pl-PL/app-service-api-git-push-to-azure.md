Użyj interfejsu wiersza polecenia platformy Azure, aby uzyskać adres URL zdalnego wdrożenia dla swojej aplikacji interfejsu API. W poniższym poleceniu zastąp ciąg *\<nazwa_aplikacji>* nazwą swojej aplikacji internetowej.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

Skonfiguruj swoje lokalne wdrożenie Git, aby było możliwe wypychanie do lokalizacji zdalnej.

```bash
git remote add azure <URI from previous step>
```

Wypchnij na zdalną platformę Azure w celu wdrożenia aplikacji. Zostanie wyświetlony monit o podanie hasła utworzonego wcześniej podczas tworzenia użytkownika wdrożenia. Upewnij się, że zostało wprowadzone hasło utworzone wcześniej podczas postępowania zgodnie z przewodnikiem szybkiego startu, a nie hasło, którego używasz do logowania się w witrynie Azure Portal.

```bash
git push azure master
```
