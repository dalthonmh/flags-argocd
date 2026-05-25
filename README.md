# flags-argocd

Demo de GitOps con ArgoCD para gestionar despliegues en Kubernetes usando la estrategia blue/green.

## ¿Qué hace?

Despliega una aplicación web (`dalthonmh/flags`) en un clúster de Kubernetes. ArgoCD monitorea este repositorio y aplica los cambios automáticamente cada vez que se hace un push.

La variable `SHOW_BUTTON` en el ConfigMap controla una funcionalidad de la app (`TRUE` / `FALSE`), lo que permite probar feature flags sin redesplegar la imagen.

## Estructura

```
application.yaml      # Recurso Application de ArgoCD
blue/
  deployment.yaml     # Deployment de la versión blue
  service.yaml        # Service expuesto en NodePort 30080
  configmap.yaml      # Feature flag: SHOW_BUTTON
```

## Requisitos

- Clúster de Kubernetes activo
- ArgoCD instalado en el namespace `argocd`
- Acceso al repositorio desde el clúster

## Despliegue

1. Registrar el repositorio en ArgoCD (si es privado).
2. Aplicar el manifiesto de la Application:

```bash
kubectl apply -f application.yaml
```

3. ArgoCD creará el namespace `flags` y desplegará todos los recursos del directorio `blue/` automáticamente.

## Acceder a la app

```bash
# Obtener la IP del nodo
kubectl get nodes -o wide

# La app estará disponible en:
http://<NODE_IP>:30080
```

## Feature flag

Para activar o desactivar el botón en la app, editar `blue/configmap.yaml`:

```yaml
data:
  SHOW_BUTTON: "TRUE" # o "FALSE"
```

Hacer commit y push. ArgoCD aplicará el cambio en segundos gracias a `selfHeal: true` y `prune: true`.
