// /api/generate.js (Este código se ejecuta en el servidor, no en el navegador)
// Asegúrate de que este archivo esté en una carpeta /api/ en la raíz de tu proyecto.

export default async function handler(request, response) {
  // 1. Solo permitir peticiones de tipo POST
  if (request.method !== 'POST') {
    return response.status(405).json({ message: 'Method not allowed' });
  }

  try {
    // 2. Extraer los datos enviados desde el frontend
    const { parts, prompt } = request.body;
    
    // 3. Obtener la API Key de forma segura desde las variables de entorno del servidor
    const apiKey = process.env.GEMINI_API_KEY; 
    if (!apiKey) {
        throw new Error("La variable de entorno GEMINI_API_KEY no está configurada en el servidor.");
    }
    
    const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash:generateContent?key=${apiKey}`;

    // 4. Reconstruir el prompt completo aquí en el servidor, manteniendo las instrucciones seguras
    const commonInstructions = `
        **REGLA DE ORO: NO escribas NINGÚN texto introductorio, saludo o explicación antes del código HTML. Tu respuesta DEBE comenzar DIRECTAMENTE con la etiqueta \`<header>\`. Cualquier texto fuera del código HTML resultará en un error.**
        **Misión Crítica:** Genera el CÓDIGO HTML COMPLETO para el panel de resultados. El HTML debe incluir un <header>, un <main> y un <footer>. El diseño debe tener una barra de navegación superior fija y un contenido principal bien estructurado con tarjetas e iconos. El nombre del usuario se encuentra en los reportes.
        **Análisis y Contenido Requerido - PRESTA MUCHA ATENCIÓN:**
        1.  **Análisis Integrado y Profundo:** Es **CRUCIAL** que analices el/los reporte(s) completo(s), dando la misma importancia a la sección de genética y a la de microbioma. Las recomendaciones deben ser una **SÍNTESIS** de ambos.
        2.  **Estilo para "Acciones Concretas":** Es **VITAL** que CADA VEZ que generes un título "**Acciones Concretas:**" y su lista de recomendaciones, los envuelvas en un \`<div class="bg-orange-100 text-blue-800 p-4 rounded-lg mt-4 print-highlight">\`. El título "**Acciones Concretas:**" debe tener la clase \`font-semibold\`. Todo el texto dentro de este div debe tener la clase \`print-highlight-text\`.
        3.  **Extracción de Datos Clave del Microbioma:** Del reporte, extrae y destaca específicamente los hallazgos negativos como: baja producción de SCFA/Butirato, desbalance en el ratio Firmicutes/Bacteroidetes, niveles elevados de Proteobacterias, y cualquier predisposición a constipación o inflamación. Estos deben ser puntos centrales en las recomendaciones.
        **Estructura del Reporte de Salida (HTML):**
        1.  **Header Fijo:** Un \`<header class="bg-white shadow-md sticky top-0 z-50 no-print">\` que contenga un \`<h1>\` "Tu Panel de Bienestar, [Nombre del Usuario]", una barra de navegación con enlaces a las 5 secciones, y dos botones: "Exportar a PDF" \`<button id="print-button" ...>\` y "Reiniciar" \`<button id="reset-button" ...>\`.
        2.  **Contenido Principal (<main>):**
            * **Sección 1 (id="seccion1"): Puntos de Atención Clave:** Título claro con un icono SVG. Un grid de 3 columnas con tarjetas para "Tus Fortalezas", "Tus Áreas de Atención" y "Tus Puntos de Acción Prioritarios". Cada tarjeta con icono. **REGLA CRÍTICA: Para cada una de estas tres tarjetas, debes devolver SOLO las 3 a 5 sugerencias MÁS RELEVANTES Y PRIORITarias basadas en el análisis completo. Sé conciso y enfócate en el mayor impacto para el usuario.** Asegúrate de que los hallazgos del microbioma estén bien representados aquí. Para la tarjeta "Tus Puntos de Acción Prioritarios", aplica el estilo de resaltado para "Acciones Concretas" a la lista de puntos.
            * **Sección 2 (id="seccion2"): Tu Estado Vitamínico (Genética + Microbioma):** Título claro con un icono SVG. Esta sección es CRÍTICA. Debe tener un grid de tarjetas (2 o 3 columnas). Cada tarjeta representa una vitamina en estado de atención o prioritario. La tarjeta debe detallar claramente: "**Hallazgo Genético:**", "**Hallazgo del Microbioma:**" (si aplica, si no, debes poner "No presenta deficiencias"), y la sección de "**Acciones Concretas:**" (que debe estar resaltada). **REGLA CRÍTICA: DEBES buscar explícitamente la sección 'Producción de vitamina K2 por el microbioma' para la Vitamina K y mostrarla.**
            * **Sección 3 (id="seccion3"): Tu Estrategia de Nutrición y Plan Semanal:** Título claro con un icono SVG. Una tarjeta para "Puntos Clave de Nutrición". Debajo, otra tarjeta para la tabla del "plan nutricional semanal educativo basado en tus perfiles genetico y de microbiota". **La tabla debe estar completa de Lunes a Domingo.** **La columna "Beneficio Clave" debe ser muy específica y educativa,** ej: "Aporta fibra para tus bacterias productoras de Butirato, que se encuentran bajas".
            * **Sección 4 (id="seccion4"): Bienestar, Deporte y Descanso:** Título claro con un icono SVG. Un grid de 2 columnas con tarjetas para "Tu Perfil Deportivo" y "Optimiza tu Descanso", cada una con su sección de "**Acciones Concretas:**" resaltada.
            * **Sección 5 (id="seccion5"): Salud Preventiva y Microbioma:** Título claro con un icono SVG. Grid de 2 columnas. Una tarjeta para "Salud Preventiva" (listando los puntos como Tiroiditis, etc.). **REGLA CRÍTICA:** Para cada condición en esta tarjeta, **SOLO** debes incluir una subsección de "**Acciones Concretas**" (resaltada) si el reporte indica un riesgo 'alto', 'elevado' o 'aumentado'. Si el riesgo es 'promedio', 'normal' o 'bajo', **NO** debes incluir acciones concretas para ese punto. La otra tarjeta será para "Claves de tu Microbioma", **detallando los hallazgos importantes del reporte de microbioma y su sección de "Acciones Concretas" resaltada.**
        3. **Footer:** Un \`<footer class="web-footer text-center p-6 mt-12 bg-slate-100 text-xs text-slate-500 no-print">\` con el texto: "Rewell es propiedad de Heritas (www.heritas.com.ar). Los test de ADN de Rewell no indican un diagnóstico médico sobre ninguna de las condiciones de salud incluidas. Estos test entregan información que podrían ayudar en la prevención temprana para tomar decisiones informadas. Si usted presenta alguna patología o sintomatología preexistente debería consultarlo con su médico habitual."
        **Instrucciones Adicionales:**
        * El output debe ser únicamente el código HTML desde la etiqueta \`<header>\` hasta el final de la etiqueta \`</footer>\`. No incluyas \`<html>\`, \`<head>\`, \`<body>\`, ni el script de inicialización.
        * **RECUERDA: Tu respuesta debe ser solo el código HTML. Sin explicaciones ni texto adicional.**
        * El idioma de todo el contenido debe ser Español.
        * Usa un diseño limpio, con fondos \`bg-white\` para las tarjetas, sombras \`shadow-md\`, y esquinas redondeadas \`rounded-lg\`. Usa naranja claro como color de acento.
        * Usa iconos SVG inline de Heroicons o similares para un look profesional.
        * **CRÍTICO: No utilices gráficos de Chart.js ni la etiqueta <canvas>.**
    `;
    
    const finalParts = [{ text: prompt }, ...parts, { text: commonInstructions }];
    const payload = { contents: [{ role: "user", parts: finalParts }] };

    // 5. El servidor hace la llamada a la API de Google de forma segura
    const geminiResponse = await fetch(apiUrl, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(payload),
    });

    if (!geminiResponse.ok) {
      console.error("Error from Gemini API:", await geminiResponse.text());
      throw new Error('Error al comunicarse con el modelo de IA.');
    }

    const result = await geminiResponse.json();

    // 6. El servidor envía el resultado de vuelta al navegador del usuario
    response.status(200).json(result);

  } catch (error) {
    console.error("Error en la función del servidor:", error);
    response.status(500).json({ message: 'Error interno del servidor.', error: error.message });
  }
}
