<template>
  <div id="app">
    <h1>Полином Лагранжа и Ньютона</h1>
    <form @submit.prevent="addPoint">
      <div>
        <label for="x">X:</label>
        <input v-model="newPoint.x" id="x" type="text" required placeholder="Введите дробное число" />
      </div>
      <div>
        <label for="y">Y:</label>
        <input v-model="newPoint.y" id="y" type="text" required placeholder="Введите дробное число" />
      </div>
      <button type="submit">Добавить точку</button>
    </form>

    <h2>Точки:</h2>
    <ul>
      <li v-for="(point, index) in points" :key="index">
        ({{ point.x }}, {{ point.y }})
        <button @click="removePoint(index)">Удалить</button>
      </li>
    </ul>

    <div v-if="points.length >= 2">
      <h2>Полином Лагранжа:</h2>
      <p>{{ lagrangePolynomial }}</p>

      <h2>Полином Ньютона:</h2>
      <p>{{ newtonPolynomial }}</p>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      points: [], // Массив для хранения точек
      newPoint: { x: '', y: '' }, // Новая точка для добавления
    };
  },
  computed: {
    lagrangePolynomial() {
      if (this.points.length < 2) return "Добавьте минимум 2 точки.";
      return this.calculateLagrange();
    },
    newtonPolynomial() {
      if (this.points.length < 2) return "Добавьте минимум 2 точки.";
      return this.calculateNewton();
    },
  },
  methods: {
    addPoint() {
      const x = parseFloat(this.newPoint.x);
      const y = parseFloat(this.newPoint.y);

      if (isNaN(x) || isNaN(y)) {
        alert("Пожалуйста, введите корректные числа.");
        return;
      }

      this.points.push({ x, y });
      this.newPoint = { x: '', y: '' };
    },
    removePoint(index) {
      this.points.splice(index, 1);
    },
    calculateLagrange() {
      const points = this.points;
      return points
        .map((p, i) => {
          const numerator = points
            .filter((_, j) => i !== j)
            .map(({ x }) => `(x - ${x.toFixed(2)})`)
            .join(" * ");
          const denominator = points
            .filter((_, j) => i !== j)
            .map(({ x }) => `(${p.x.toFixed(2)} - ${x.toFixed(2)})`)
            .reduce((a, b) => `${a} * ${b}`);
          return `(${p.y.toFixed(2)} * (${numerator}) / (${denominator}))`;
        })
        .join(" + ");
    },
    calculateNewton() {
      const points = this.points;
      const n = points.length;

      const dividedDifferences = Array(n)
        .fill(null)
        .map(() => Array(n).fill(0));

      // Заполнение начальных значений
      for (let i = 0; i < n; i++) {
        dividedDifferences[i][0] = points[i].y;
      }

      // Вычисление разделённых разностей
      for (let j = 1; j < n; j++) {
        for (let i = 0; i < n - j; i++) {
          dividedDifferences[i][j] =
            (dividedDifferences[i + 1][j - 1] -
              dividedDifferences[i][j - 1]) /
            (points[i + j].x - points[i].x);
        }
      }

      // Формирование полинома
      let result = `${dividedDifferences[0][0].toFixed(2)}`;
      for (let i = 1; i < n; i++) {
        const terms = points
          .slice(0, i)
          .map(({ x }) => `(x - ${x.toFixed(2)})`)
          .join(" * ");
        result += ` + (${dividedDifferences[0][i].toFixed(2)} * ${terms})`;
      }

      return result;
    },
  },
};
</script>

<style>
body {
  font-family: Arial, sans-serif;
  margin: 20px;
}
form {
  margin-bottom: 20px;
}
button {
  margin-left: 10px;
}
</style>
