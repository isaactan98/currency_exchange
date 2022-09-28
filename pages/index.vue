<template>
  <main class="md:w-3/5 mx-auto">
    <div class="min-h-screen px-4 xl:px-2">
      <!-- <div class="hero-overlay bg-opacity-60"></div> -->
      <div class="mt-8 xl:mt-0">
        <div class="text-center mb-8 xl:mb-12">
          <h1 class="text-3xl xl:text-5xl font-bold mb-4">
            The World's Trusted Currency Authority
          </h1>
          <p class="text-xs xl:text-base">
            Check exchange rates, and free currency tools
          </p>
        </div>
        <div
          class="border border-stone-200 dark:border-stone-700 rounded-md p-8 xl:p-12 shadow-md"
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
            class="grid xl:flex justify-between grid-cols-1 gap-4 mt-4"
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
                <optgroup v-for="(c, k) in country" :key="k" :label="k">
                  <option :value="k" v-for="(s, k) in c" :key="k">
                    {{ s }}
                  </option>
                </optgroup>
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
                <optgroup v-for="(c, k) in country" :key="k" :label="k">
                  <option :value="k" v-for="(s, k) in c" :key="k">
                    {{ s }}
                  </option>
                </optgroup>
              </select>
              <div class="btn btn-ghost loading" v-else></div>
            </div>
          </div>
          <div class="mt-6">
            <div class="font-bold mb-6 hidden" id="result_col">
              <p class="text-lg xl:text-2xl">
                <span id="ori">1.00 US Dollar</span> =
              </p>
              <p class="text-2xl xl:text-5xl" id="result">1.00 US Dollar</p>
            </div>
            <div class="flex justify-end">
              <button class="btn btn-primary w-full xl:w-auto" id="convert">
                Convert
              </button>
            </div>
            <div id="show_one" class="text-xs mt-4 hidden"></div>
          </div>
          <div class="toast hidden" id="error">
            <div class="alert alert-error">
              <div>
                <span id="error_msg">New message arrived.</span>
              </div>
            </div>
          </div>
          <div>
            <p class="text-xs mt-4 text-justify xl:text-left">
              <span class="font-bold">Disclaimer:</span> This is a demo
              application. The exchange rates are provided by third-party
              providers and are subject to change. The exchange rates are
              provided for informational purposes only and do not constitute
              financial advice of any kind. We do not guarantee the accuracy of
              the exchange rates.
            </p>
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
    const show_one = document.getElementById("show_one");

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
        var arr = {};

        fetch(link + "symbols", keys(e.target.value))
          .then((response) => response.text())
          .then((result) => {
            var countries = JSON.parse(result);
            Object.entries(countries.symbols).map(([key, value]) => {
              if (arr[value.charAt(0)] != null) {
                arr[value.charAt(0)][key] = value;
              } else {
                arr[value.charAt(0)] = {};
                arr[value.charAt(0)][key] = value;
              }
            });
            this.country = Object.fromEntries(Object.entries(arr).sort());
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

                    if (amount.value > 1) {
                      var one = [];
                      var string = "";

                      show_one.classList.remove("hidden");

                      fetch(
                        link + `convert?to=${to}&from=${from}&amount=1`,
                        keys(e.target.value)
                      )
                        .then((response) => response.text())
                        .then((r) => {
                          this.calculate = JSON.parse(r);
                          one.push(
                            `<p>1 ${from} = ${this.calculate.result.toLocaleString()} ${to}</p>`
                          );

                          string = `<p>1 ${from} = ${this.calculate.result.toLocaleString()} ${to}</p><p>1 ${to} = ${
                            1 / this.calculate.result.toLocaleString()
                          } ${from}</p>`;
                          show_one.innerHTML = string;
                        })
                        .catch((error) => {
                          error_col.classList.remove("hidden");
                          error_msg.innerHTML = error;
                          setTimeout(() => {
                            error_col.classList.add("hidden");
                          }, 5000);
                        });
                    } else {
                      show_one.classList.add("hidden");
                    }
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
