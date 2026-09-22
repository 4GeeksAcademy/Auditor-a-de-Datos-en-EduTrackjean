# EduTrack — Informe de Auditoría de Datos

## Resumen Ejecutivo
Este informe documenta los resultados obtenidos tras auditar y limpiar la tabla `enrollments` en la base de datos de EduTrack. Se evaluó el estado de las inscripciones, se corrigieron inconsistencias de datos, se removieron cuentas de prueba y se calcularon agregaciones e ingresos finales.

---

## 1. Inscripciones en Intro to Python

**Filtro:** `course_title = 'Intro to Python'`  
**Resultado:** 5 inscripciones encontradas.

| student_name | student_email | completion_percentage |
| :--- | :--- | :--- |
| Emily Watson | emily.watson@student.edutrack.com | 85 |
| Klaus Weber | klaus.weber@student.edutrack.com | 92 |
| Marco Rossi | marco.rossi@student.edutrack.com | 88 |
| James Miller | james.miller@test.com | 30 |
| Priya Sharma | priya.sharma@student.edutrack.com | 55 |

---

## 2. Posibles Abandonos

**Filtro:** `completion_percentage < 10`  
**Resultado:** 4 registros detectados con un avance menor al 10%.

| student_name | course_title | completion_percentage |
| :--- | :--- | :--- |
| Lucia Fernandes | Web Design Basics | 5 |
| Lucia Fernandes | Digital Marketing 101 | 3 |
| Yuki Nakamura | UI/UX Fundamentals | 0 |
| Pierre Dubois | UI/UX Fundamentals | 0 |

---

## 3. Inscripciones sin Instructor

**Filtro:** `instructor IS NULL`  
**Resultado:** 2 registros con instructor no asignado.

| student_name | course_title | instructor |
| :--- | :--- | :--- |
| Yuki Nakamura | UI/UX Fundamentals | *NULL* |
| Pierre Dubois | UI/UX Fundamentals | *NULL* |

---

## 4. Top 5 Estudiantes No Aprobados con Mayor Progreso

**Filtro/Orden:** `passed = false ORDER BY completion_percentage DESC LIMIT 5`  
**Resultado:** 5 estudiantes destacados que están próximos a aprobar o con mayor avance acumulado.

| student_name | course_title | completion_percentage | passed |
| :--- | :--- | :--- | :--- |
| Emily Watson | Web Design Basics | 60 | false |
| Priya Sharma | Intro to Python | 55 | false |
| Yuki Nakamura | Data Analysis with SQL | 45 | false |
| Emily Watson | Advanced Python | 40 | false |
| James Miller | Intro to Python | 30 | false |

---

## 5. Inscripciones Creadas en el Último Año

**Filtro:** `enrollment_date >= CURRENT_DATE - INTERVAL '1 year'`  
**Resultado:** 0 inscripciones.

*Nota: Los registros almacenados en el dataset de partida corresponden a fechas históricas anteriores al cálculo de `CURRENT_DATE - INTERVAL '1 year'`.*

---

## 6. Inscripción Faltante Agregada

**Acción:** `INSERT INTO enrollments` (ID: 18)  
**Resultado:** Se insertó correctamente 1 nuevo registro.

- **ID:** 18
- **Estudiante:** Lucia Fernandes (`student_id`: 3)
- **Curso:** Advanced Python (`course_id`: 5)
- **Categoría:** Programming
- **Fecha de inscripción:** 2025-04-01
- **Avance / Estado:** 0% / false
- **Monto pagado:** $69.99
- **Instructor:** Carlos Vega

---

## 7. Corrección de Instructores NULL

**Acción:** `UPDATE enrollments SET instructor = 'Pending assignment' WHERE instructor IS NULL`  
**Resultado:** 2 registros actualizados exitosamente.

- Registros modificados: Yuki Nakamura y Pierre Dubois (`UI/UX Fundamentals`).
- Nuevo valor asignado: `'Pending assignment'`.

---

## 8. Eliminación de Cuentas de Prueba

**Acción:** `DELETE FROM enrollments WHERE student_email LIKE '%@test.com'`  
**Resultado:** 2 cuentas de prueba eliminadas.

- **Registros removidos:** James Miller (`james.miller@test.com`) y Alex Chen (`alex.chen@test.com`).
- **Total de registros finales en la base:** 16 inscripciones.

---

## 9. Inscripciones por Categoría

**Consulta:** `COUNT(*)` agrupado por `category` (después de la limpieza).  
**Resultado:**

| category | total_enrollments |
| :--- | :--- |
| Programming | 7 |
| Design | 4 |
| Data | 3 |
| Marketing | 2 |

---

## 10. Promedio de Completado por Curso

**Consulta:** `AVG(completion_percentage)` agrupado por `course_title` en orden ascendente.  
**Resultado:**

| course_title | average_completion |
| :--- | :--- |
| UI/UX Fundamentals | 0.00% |
| Web Design Basics | 32.50% |
| Digital Marketing 101 | 36.50% |
| Advanced Python | 45.00% |
| Data Analysis with SQL | 47.67% |
| Intro to Python | 80.00% |

---

## 11. Cursos con Más de 3 Inscripciones

**Consulta:** `HAVING COUNT(*) > 3`  
**Resultado:** 1 único curso cumple el criterio.

| course_title | total_enrollments |
| :--- | :--- |
| Intro to Python | 4 |

---

## 12. Ingresos Totales por Categoría

**Consulta:** `SUM(monthly_fee_paid)` agrupado por `category` en orden descendente.  
**Resultado:**

| category | total_revenue |
| :--- | :--- |
| Programming | $409.93 |
| Data | $179.97 |
| Design | $169.96 |
| Marketing | $59.98 |