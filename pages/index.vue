<template>
  <main class="container mx-auto">
    <div class="hero min-h-screen px-3 lg:px-0">
      <!-- <div class="hero-overlay bg-opacity-60"></div> -->
      <div class="">
        <div class="text-center mb-12">
          <h1 class="text-3xl lg:text-5xl font-bold mb-4">
            The World's Trusted Currency Authority
          </h1>
          <p class="text-xs lg:text-base">
            Check exchange rates, send money internationally, and free currency
            tools
          </p>
        </div>
        <div
          class="border border-stone-200 dark:border-stone-500 rounded-md p-12 shadow-md"
        >
          <div class="grid lg:flex justify-between grid-cols-1 gap-4">
            <div class="form-control w-full max-w-xs">
              <label class="label">
                <span class="label-text font-bold">Amount</span>
              </label>
              <input
                type="text"
                placeholder="Type here"
                class="input input-bordered rounded-md w-full max-w-xs"
                id="amount"
                autocomplete="off"
              />
            </div>
            <div class="form-control w-full max-w-xs">
              <label class="label">
                <span class="label-text font-bold">From</span>
              </label>
              <select
                name=""
                class="select select-bordered rounded-md"
                id="from_ipt"
                v-if="country != null"
              >
                <option :value="k" v-for="(s, k) in country.symbols" :key="k">
                  {{ s }}
                </option>
              </select>
              <div class="btn btn-ghost loading" v-else></div>
            </div>
            <div class="form-control w-full max-w-xs">
              <label class="label">
                <span class="label-text font-bold">To</span>
              </label>
              <select
                name=""
                class="select select-bordered rounded-md"
                id="to_ipt"
                v-if="country != null"
              >
                <option :value="k" v-for="(s, k) in country.symbols" :key="k">
                  {{ s }}
                </option>
              </select>
              <div class="btn btn-ghost loading" v-else></div>
            </div>
          </div>
          <div class="mt-6">
            <div class="font-bold mb-6 hidden" id="result_col">
              <p class="text-lg lg:text-2xl">
                <span id="ori">1.00 US Dollar</span> =
              </p>
              <p class="text-2xl lg:text-5xl" id="result">1.00 US Dollar</p>
            </div>
            <div class="flex justify-end">
              <button class="btn btn-primary" id="convert">Convert</button>
            </div>
          </div>
          <div class="toast hidden" id="error">
            <div class="alert alert-error">
              <div>
                <span id="error_msg">New message arrived.</span>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </main>
</template>

<script>
export default {
  name: "HOME",
  data() {
    return {
      country: null,
      calculate: null,
    };
  },
  mounted() {
    const error_col = document.getElementById("error");
    const error_msg = document.getElementById("error_msg");

    var myHeaders = new Headers();
    myHeaders.append("apikey", process.env.FIXER_API);

    var requestOptions = {
      method: "GET",
      redirect: "follow",
      headers: myHeaders,
    };

    fetch("https://api.apilayer.com/fixer/symbols", requestOptions)
      .then((response) => response.text())
      .then((result) => {
        this.country = JSON.parse(result);
        console.log(this.country);
      })
      .then(() => {
        const convert = document.getElementById("convert");
        const amount = document.getElementById("amount");
        const from_ipt = document.getElementById("from_ipt");
        const to_ipt = document.getElementById("to_ipt");
        const result = document.getElementById("result");
        const ori = document.getElementById("ori");
        const result_col = document.getElementById("result_col");

        convert.addEventListener("click", () => {
          convert.classList.add("loading");

          const from = from_ipt.value;
          const to = to_ipt.value;

          if (from === "" || to === "") {
            alert("Please select a currency");
            convert.classList.remove("loading");
          } else {
            fetch(
              `https://api.apilayer.com/fixer/convert?to=${to}&from=${from}&amount=${amount.value}`,
              requestOptions
            )
              .then((response) => response.text())
              .then((r) => {
                console.log(r);
                this.calculate = JSON.parse(r);
                console.log(this.calculate);
                result_col.classList.remove("hidden");
                ori.innerHTML = `${amount.value} ${from}`;
                result.innerHTML = `${this.calculate.result} ${to}`;
                convert.classList.remove("loading");
              })
              .catch((error) => {
                error_col.classList.remove("hidden");
                error_msg.innerHTML = error;
              });
          }
        });
      })
      .catch((error) => {
        console.log("error", error);
        error_col.classList.remove("hidden");
        error_msg.innerHTML = error;
      });
  },
};
</script>
