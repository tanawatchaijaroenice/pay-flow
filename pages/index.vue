<template>
  <div class="w-full px-8 py-4 gap-4">
    <div class="w-full py-6">
      <p class="text-center text-white text-4xl font-bold">Pay Flow</p>
    </div>
    <div class="w-full">
      <div class="w-[100%]">
        <p>add person</p>
        <div class="flex">
          <input class="w-full" type="text" v-model="name" />
          <button
            @click="addPerson"
            class="bg-[#1c9e41] rounded-xl w-20 font-bold text-white"
          >
            submit
          </button>
        </div>
      </div>
    </div>
    <div class="w-full">
      <div class="w-[100%] gap-1">
        <p>List Person:</p>
        <div v-for="(item, index) in person" :key="index" class="flex gap-2">
          <p>
            {{ item.name }}
          </p>
          <button
            @click="removePerson(index)"
            class="bg-[#eb2a2a] rounded-xl w-10 font-bold text-white"
          >
            del
          </button>
        </div>
      </div>
    </div>
    <div class="py-3">
      <button
        @click="addEntry"
        class="bg-sky-500 rounded-xl w-20 font-bold text-white"
      >
        Add
      </button>
    </div>
    <div class="flex gap-3 py-2" v-for="(item, index) in form" :key="index">
      <div class="w-[30%]">
        <p>Name</p>
        <select v-model="item.id" class="w-full">
          <option v-for="(item, index) in person" :key="index" :value="item.id">
            {{ item.name }}
          </option>
        </select>
      </div>
      <div class="w-[30%]">
        <p>Expense</p>
        <input class="w-full" type="text" v-model="item.expense" />
      </div>
      <div class="w-[30%]">
        <p>Amount</p>
        <input class="w-full" type="text" v-model="item.amount" />
      </div>
      <div class="w-[10%] flex justify-center items-end">
        <button
          @click="remove(index)"
          class="bg-[red] rounded-lg w-full font-bold text-white"
        >
          Del
        </button>
      </div>
    </div>
    <div>
      <button
        @click="calculate()"
        class="bg-[#328d0c] rounded-lg w-full font-bold text-white"
      >
        Calculate
      </button>
    </div>
  </div>
</template>

<script setup>
const name = ref();
const person = ref([
  { id: 0, name: "ice" },
  { id: 1, name: "pookie" },
]);
const form = ref([
  { id: 0, name: "", expense: "", amount: null },
  { id: 0, name: "", expense: "", amount: null },
]);

const addPerson = () => {
  person.value.push({
    id: person.value.length == 0 ? 0 : person.value.length,
    name: name.value,
  });
  name.value = null;
  console.log("person.value", person.value);
};
const removePerson = (index) => {
  person.value.splice(index, 1);
};

const addEntry = () => {
  form.value.push({
    id: 0,
    name: "",
    expense: "",
    amount: null,
  });
  console.log("addEntry", form.value);
};

const remove = (index) => {
  form.value.splice(index, 1);
};

const sum = computed(() => {
  let sum = 0;
  form.value.forEach((item) => {
    sum += Number(item.amount);
  });
  return sum;
});

const perAmount = computed(() => {
  const perAmt = sum.value / person.value.length;
  return perAmt;
});

const calculate = () => {
  const result = Object.keys(
    form.value.reduce((acc, item) => {
      acc[item.id] = (acc[item.id] || 0) + parseInt(item.amount);
      return acc;
    }, {})
  ).map((id) => {
    const sumPerson = form.value
      .filter((item) => item.id === parseInt(id))
      .reduce((sum, item) => sum + parseInt(item.amount), 0);

    return {
      id: parseInt(id),
      sumPerson: sumPerson, // sumPerson ถูกคำนวณจาก filter และ reduce
      payPerson: sumPerson - perAmount.value, // payPerson หรืออาจจะใช้ชื่ออื่นก็ได้ถ้าจำเป็น
    };
  });

  console.log("result", result);

  console.log("calculate sum", sum.value);
  console.log("calculate perAmount", perAmount.value);
};

watch(form.value, () => {
  console.log("form updated:", form.value);
});
</script>
