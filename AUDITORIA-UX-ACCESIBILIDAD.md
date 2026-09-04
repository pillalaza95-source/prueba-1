# Auditoría UX y accesibilidad

**Proyecto:** Prueba1  
**Superficie revisada:** `index.html`, `styles.css`  
**Fecha:** 4 de septiembre de 2026  
**Auditor:** OpenCode (revisión asistida; informe consolidado sobre los archivos reales)

## Resumen ejecutivo

La página tiene una dirección visual clara, una jerarquía editorial reconocible y un breakpoint móvil bien definido. El enlace principal lleva a la sección de datos y la imagen incluye texto alternativo. No se encontraron formularios, controles complejos ni estados de error que auditar.

Los principales riesgos están en la experiencia con teclado y lectores de pantalla: no existe un estilo de foco visible, los bloques de datos usan `div` en lugar de una estructura de definición semántica y la sección de estadísticas presenta números sin encabezados individuales. También se debe verificar el contraste de los textos secundarios y probar la carga de las imágenes desde una red lenta o sin conexión.

**Prioridad recomendada:** corregir primero el foco visible y la semántica de datos; después validar contraste con una herramienta WCAG y mejorar la resiliencia de recursos externos.

## Hallazgos

### Alto

#### A11Y-01: No hay un estado de foco visible

- **Evidencia:** `styles.css` define `.primary-action:hover` implícitamente solo por el comportamiento del navegador, pero no define `:focus` ni `:focus-visible` para enlaces o elementos interactivos.
- **Impacto:** las personas que navegan con teclado pueden perder la posición actual y no saber qué elemento está seleccionado.
- **Recomendación:** añadir un estilo consistente para `:focus-visible`, por ejemplo un contorno de 3 px con suficiente contraste y separación mediante `outline-offset`.

```css
.primary-action:focus-visible {
  outline: 3px solid var(--ink);
  outline-offset: 4px;
}
```

### Medio

#### A11Y-02: La ficha de datos no expresa relaciones de término y valor

- **Evidencia:** en `index.html`, `.facts-grid` contiene pares visuales `span` + `strong` dentro de `div`.
- **Impacto:** un lector de pantalla recibe texto, pero no una relación semántica explícita entre cada etiqueta y su valor.
- **Recomendación:** usar `<dl>`, `<dt>` y `<dd>` para nombre, posición, nacionalidad, fecha, club y dorsal.

#### A11Y-03: Las tarjetas estadísticas no tienen encabezados propios

- **Evidencia:** cada `.stat-card` usa un `<p>` para el nombre de la métrica y un `<strong>` para el valor; no existe un encabezado por tarjeta.
- **Impacto:** se dificulta saltar y comprender cada métrica al navegar con un lector de pantalla.
- **Recomendación:** cambiar el nombre de cada métrica a `<h3>` y mantener las tarjetas dentro de una sección con un encabezado real (`h2`).

#### A11Y-04: Jerarquía de encabezados incompleta

- **Evidencia:** existe un `h1`, pero los títulos visuales "Ficha del jugador" y "Números que hablan" están construidos con `<p>`.
- **Impacto:** la navegación por encabezados no representa la estructura visual de la página.
- **Recomendación:** usar un `<h2>` para cada sección y reservar los `<h3>` para las métricas individuales.

#### UX-01: Recursos visuales externos sin estrategia de resiliencia

- **Evidencia:** la foto principal depende de Wikimedia y el `onerror` cambia a una imagen de Unsplash; ambas requieren red.
- **Impacto:** en modo offline o con bloqueo de terceros puede aparecer una imagen ausente o una composición incompleta. El cambio inline también puede dificultar el diagnóstico del fallo.
- **Recomendación:** descargar una imagen con licencia compatible al proyecto, usar una ruta local y conservar un fondo/estado visual útil si la imagen falla. Documentar la atribución cuando corresponda.

#### UX-02: El contenido actualizado puede quedar desfasado

- **Evidencia:** el pie muestra `Prueba1 · 2025` y el bloque de actualización muestra `Temporada 2024/25`.
- **Impacto:** la interfaz puede comunicar una fecha incorrecta si se reutiliza el perfil.
- **Recomendación:** generar o revisar el año desde una única fuente de datos y añadir una fecha visible de actualización solo cuando sea verificable.

### Bajo

#### A11Y-05: Elementos decorativos no están marcados explícitamente

- **Evidencia:** los `span` decorativos del punto verde y del separador `•` no tienen `aria-hidden="true"`.
- **Impacto:** normalmente es menor, pero algunos lectores pueden anunciar caracteres sin valor informativo.
- **Recomendación:** marcar los adornos como `aria-hidden="true"` y mantener el texto informativo fuera de ellos.

#### UX-03: Falta un enlace para saltar al contenido

- **Evidencia:** la página comienza directamente con el contenido principal y no ofrece un enlace "Saltar al contenido".
- **Impacto:** el coste es pequeño en esta página, pero aumenta para usuarios de teclado si se añaden navegación o más elementos antes del contenido.
- **Recomendación:** añadir un skip link al inicio y un `id` en el contenido principal.

#### A11Y-06: La información de color no debe ser la única señal

- **Evidencia:** el bloque destacado de estadísticas se diferencia principalmente con el fondo lima.
- **Impacto:** personas con baja visión cromática podrían no percibir la diferencia de prioridad.
- **Recomendación:** conservar también una etiqueta textual, icono o jerarquía explícita. La etiqueta actual ayuda, pero conviene verificarlo en pruebas visuales.

## Aspectos positivos

- `lang="es"`, `charset` y `viewport` están correctamente declarados.
- Existe un único `h1` identificable para el perfil.
- La foto tiene `alt` descriptivo en español.
- El enlace "Ver perfil" tiene un destino interno claro (`#datos`).
- El diseño incluye breakpoints a 760 px y 420 px y reduce las columnas en pantallas pequeñas.
- No se usa texto dentro de imágenes para transmitir la información principal.
- La página no depende de JavaScript para mostrar el contenido esencial.

## Plan de corrección priorizado

1. Añadir `:focus-visible` para el enlace y cualquier control futuro.
2. Cambiar títulos de sección a `h2`, títulos de métrica a `h3` y la ficha a `dl/dt/dd`.
3. Medir el contraste de `--muted`, `#75814d` y `#596329` con WCAG; ajustar colores si no alcanzan 4.5:1 para texto normal.
4. Sustituir las imágenes remotas por un recurso local con licencia documentada o definir un estado de carga/error accesible.
5. Añadir skip link y ocultar adornos con `aria-hidden`.
6. Probar con teclado, zoom al 200%, lector de pantalla y viewport de 320 px de ancho.

## Límites de esta auditoría

Esta revisión fue estática. No sustituye una prueba manual con NVDA/JAWS/VoiceOver, una medición automatizada de contraste ni una comprobación visual en varios navegadores. No se modificaron `index.html` ni `styles.css`.
