# 🧪 Testing del Frontend (Angular + Vitest)

Este documento explica cómo ejecutar los tests del proyecto, cómo está configurado Vitest, qué workarounds se aplicaron y por qué, además de buenas prácticas para mantener la suite estable.

## 🚀 1. Ejecutar los tests

### Tests normales (sin cobertura)

- npm run test

   Tests en modo CI (sin watch)

- npm run test:ci

   Tests con cobertura

- npm run test:coverage

La cobertura se genera con el proveedor V8 y produce: reporte en consola y archivo lcov para integraciones con GitHub Actions o herramientas externas.

## 🧰 2. Configuración de Vitest

El proyecto usa Vitest como framework de testing por su rapidez y compatibilidad con TypeScript.

Ventajas:

  - Ejecución muy rápida

  - Cobertura con motor V8

  - Reportes modernos

  - Integración sencilla con Angular

Dependencias clave: vitest, @angular/platform-browser-dynamic, jsdom (si se usa DOM testing).

## 🧪 3. Tipos de tests incluidos

✔ Unit tests

Comprueban la lógica interna de componentes y servicios.

✔ Tests DOM

Verifican que la interfaz muestre: mensajes de error, mensajes de éxito, elementos visibles y estados del formulario.

Se usan selectores estables como: html

<div data-test="form-message">...</div>

✔ Tests con mocks

Se simulan servicios, HTTP y router para evitar dependencias externas.

Ejemplos: of() para respuestas correctas, throwError() para errores y spies para controlar llamadas.

## 🛠 4. Workarounds aplicados (Vitest + Angular)

Vitest todavía no resuelve bien recursos externos de Angular, así que se aplicaron soluciones temporales.

🔧 4.1 Inline de templates y styles

Se inlinearon templates y estilos en: AppComponent y ProductListComponent.

Motivo: Vitest no resuelve correctamente archivos HTML/SCSS externos.

🔧 4.2 Instanciación manual de componentes

En algunos casos se evita createComponent para prevenir errores NG0202.

Ejemplo: 

ts

const component = new ProductListComponent(mockService);

Motivo: Angular DI falla bajo Vitest en ciertos escenarios.

🔧 4.3 Reemplazo de HttpTestingController

Se sustituyó por spies:

ts

spyOn(mockService, 'login').and.returnValue(of(...));

Motivo: HttpTestingController no es totalmente compatible con Vitest.

🔧 4.4 Uso de resolveComponentResources

Se añadió como medida defensiva en componentes con overrideComponent.

## 📊 5. Cobertura

La cobertura se genera con: npx vitest run --coverage.

Resultados actuales: 19 tests pasando (100%), cobertura completa generada con V8 y reporte lcov disponible para GitHub Actions.

## 🤖 6. GitHub Actions

El workflow ejecuta:

  - npm ci

  - npm run test:ci

  - genera cobertura

  - sube el reporte lcov

Esto garantiza que: no se sube código roto, la cobertura se mantiene y los tests se ejecutan automáticamente.

## 🧼 7. Scripts útiles

Formateo

npm run format

Typecheck

npm run typecheck

Tests con cobertura

npm run test:coverage

## 📘 8. Notas finales

Los workarounds aplicados están documentados para facilitar su eliminación futura cuando:

  - Angular mejore compatibilidad con Vitest

  - Vitest implemente soporte completo para recursos externos

  - Se migre a un entorno de testing más integrado

Mientras tanto, la suite es estable, rápida y con cobertura completa.
