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
          <div class="form-control col-span-1">
            <label for="" class="label">
              <span class="label-text font-bold">Provider</span>
            </label>
            <select
              name=""
              id="provider"
              class="select select-bordered rounded-md"
            >
              <option value="">Select Provider</option>
              <option
                :value="p.name"
                v-for="(p, k) in provider"
                :key="k"
                :data-link="p.link"
              >
                {{ p.name }}
              </option>
            </select>
          </div>

          <div
            class="grid lg:flex justify-between grid-cols-1 gap-4 mt-4"
            id="exchange"
            style="display: none"
          >
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
              <button class="btn btn-primary w-full lg:w-auto" id="convert">
                Convert
              </button>
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
      provider: [
        {
          name: "Rapid Api",
          link: "https://currency-conversion-and-exchange-rates.p.rapidapi.com/",
        },
        {
          name: "Fixer",
          link: "https://api.apilayer.com/fixer/",
        },
        {
          name: "ExchangeRate",
          link: "https://api.apilayer.com/exchangerates_data/",
        },
      ],
    };
  },
  mounted() {
    const provider = document.getElementById("provider");
    const exchange = document.getElementById("exchange");
    const error_col = document.getElementById("error");
    const error_msg = document.getElementById("error_msg");

    const keys = (p) => {
      if (p != "Rapid Api") {
        var myHeaders = new Headers();
        myHeaders.append("apikey", process.env.FIXER_API);

        var requestOptions = {
          method: "GET",
          redirect: "follow",
          headers: myHeaders,
        };

        return requestOptions;
      } else {
        const requestOptions = {
          method: "GET",
          headers: {
            "X-RapidAPI-Key": process.env.RAPID_API,
            "X-RapidAPI-Host":
              "currency-conversion-and-exchange-rates.p.rapidapi.com",
          },
        };

        return requestOptions;
      }
    };

    provider.addEventListener("change", (e) => {
      if (e.target.value != null) {
        const link = e.target.options[e.target.selectedIndex].dataset.link;

        exchange.style.display = "";

        fetch(link + "symbols", keys(e.target.value))
          .then((response) => response.text())
          .then((result) => {
            this.country = JSON.parse(result);
            // console.log(this.country);
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
                  link + `convert?to=${to}&from=${from}&amount=${amount.value}`,
                  keys(e.target.value)
                )
                  .then((response) => response.text())
                  .then((r) => {
                    var av = amount.value;
                    this.calculate = JSON.parse(r);

                    result_col.classList.remove("hidden");
                    ori.innerHTML = `${av.toLocaleString()} ${from}`;
                    result.innerHTML = `${this.calculate.result.toLocaleString()} ${to}`;
                    convert.classList.remove("loading");
                  })
                  .catch((error) => {
                    error_col.classList.remove("hidden");
                    error_msg.innerHTML = error;
                    setTimeout(() => {
                      error_col.classList.add("hidden");
                    }, 5000);
                  });
              }
            });
          })
          .catch((error) => {
            error_col.classList.remove("hidden");
            error_msg.innerHTML = error;
            setTimeout(() => {
              error_col.classList.add("hidden");
            }, 5000);
          });
      }
    });
  },
};
</script>
