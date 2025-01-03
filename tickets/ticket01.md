## 1.1 Общий вид метрического классификатора. Близость к классу. kNN, Radius Neighbors. Leave-one-out error.
[Момент в лекции](https://youtu.be/AySw5WqkEKo?si=OrnawN2F2sZ4kTn8&t=3171)\
D - наши данные\
$x_i$ - один набор фичей\
**Метрический классификатор** - означает, что считаем расстояние между объектами и как-то по этому классифицируем\
Общей вид: $h(x; D) = argmax_{y \in Y}(\sum_{x_i \in D} 1_{y_i=y} * w(x_i, x))$\
Такая сумма $Г_y(x) = \sum_{x_i \in D} 1_{y_i=y} * w(x_i, x)$ называется **близостью к классу y**.

### Пример:
Возьмём Евклидово расстояние и для самой ближайшей точке к x, назовём её z, зададим значение w(x, z) = 1. Для всех остальных w(x,k)=0. То есть\
$w(x,z) = 1$, if $z = argmin_v(\rho(v, x))$\
$w(x,z) = 0$, else\
Теперь наш классификатор новой точке присваивает тот же класс, что и у самой ближней к нему.\
\
Но это может быть не всегда корректно, давайте смотреть на k ближайшех соседей - получили **kNN (k-nearest-neighbors)**\
$h(x; D) = argmax_{y \in Y}(\sum_{x_i \in D} 1_{y_i=y} * w(x_i, x))$\
$w(x_i, x) = 1, if x_i$ - один из k-ближайших соседей x\
$w(x_i, x) = 1, if distance \rho(x_i, x) < R$, то же самое, что и выше, только берём не k соседей, а всех кто лежит в заданном радиусе, такое R называем **Radius Neighbors**.\
**Важно:** векторное пространство (ВП) не нужно, только заданное растояние. Например расстояние между строками считать умеем, а ВП нет.\
\
<img src=https://github.com/BrudLord/ml-questions/blob/6d505d0f9b2e7e3fb2c327718e5786a2b6bea740/tickets/images/tickets01_1.png alt="kNN" width="300" align="center">\
Если у нас получилось поравну нескольких классов, то чтобы конкретезировать будем брать первый по алфавиту или любой другой детерменнированной стратегии (чтобы всё повторялось).\
\
Для определения лучшего k (сколько брать соседей), будем смотреть на ошибку **Leave-one-out error** (LOO)\
$LOO(k, D) = \frac{\sum_{x_i \in D} [h(x_i;D \backslash x_i;k) \neq y_i]}{|D|}$, то есть поочереди выкинули из данных каждую строчку и посмотрели сколько раз не угадали. 0 - всё угадали, 1 - ничего не угадали.

## 1.2 Регрессия. LASSO, LARS, Elastic Net.
[Момент в лекции](https://youtu.be/oJ_cnAQ3ViA?si=28pfkUkypVRMcog2&t=4362)\
Этот билет продолжает разговор о регулезации в билете 8.2 (гребневая регрессия).\
Хотим уменьшить variance. Для этого используем **LASSO (Least Absolute Shrinkage and Selection Operator)**\
$L_{lasso} = ||Xw-Y||_2^2 + \alpha * ||w||_1$, то есть к обычно регрессии прибавляем абсолютные значения весов. Это усложняет решение и зануляет фичи. Для объяснения второго можно воспользоваться геометрическим доказательством (фото). ||w|| - выглядит как ромб, а ||Xw-y|| - как элипс. Их контакт будет в вершине ромба. Хотим контакт, так как мы минимизируем сумму и если касания нет, то можем что-то уменьшить, сохранив другое и тогда общая сумма уменьшится.\
\
<img src=https://github.com/BrudLord/ml-questions/blob/2ab7fbb67c6da4a7f15724274487f0ef8bcd9833/tickets/images/tickets01_2.png alt="LASSO" width="600" align="center">\
Для решения LASSO используем **LARS (Least Angle regression)**:
1) Берём одну фичу $x_i$, у которой наибольшая коореляция с y. (Если нарисовать в виде вектором, то это вича с наименьшим углом)
2) Будем собирать нашу итоговую функцию по чуть-чуть. Сейчас он равна $h = \beta_1 * x_i$
3) Увеличиваем $\beta$, пока не найдем другую фичу с большей коореляцей, назовём её $x_j$ и зададим её $\beta_2$
4) Повторяем. Все предыдущие бетты, кроме последней фиксируем.
5) Останавливаемся, когда сумма коэффициентов (умноженная на $\alpha$) превысит уменьшение ошибки

**Elastic Net** представляет из себя объединение L1 и L2 (L1 из Lasso и L2 из гребневой регрессии):\
$L_{elastic} = ||Xw-Y||_2^2 + \alpha*(1-p)*||w||_2^2 + \alpha\*p\*||w||_1$, здесь есть 2 коэффициента $\alpha$ - сила штрафа за фичи и p - доля L1 регулезации.\
\
\
### Предсказание на доп вопросы:\
$R^2-score$\
$R^2 = 1 - \frac{u}{v}$\
$u = \sum (h(x_i) - y_i)^2$, это MSE нашей функции\
$v = \sum (y^- - y_i)^2$, где $y^- = \frac{1}{N}\sum y_i$, то есть v - это MSE при константе как предсказании\
При чём тут процент объяснённой дисперсии?\
$R^2 = 1 - \frac{u}{v} = \frac{v-u}{v}$\
v - дисперсия, то есть в знаменателе остаётся столько, сколько мы объяснили. Если u приближается к v, то ничего не объяснили. По факту ничего этог нет, но можно так думать.
