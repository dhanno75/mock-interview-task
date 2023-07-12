let url =
  "https://gitlab.com/gvanderput/gerard-movie-filtering/-/raw/master/data/movies.json";
let mainData = {};
const table = document.createElement("table");
document.getElementById("main").append(table);
const findYear = document.getElementById("year");
const movieTitle = document.getElementById("movieTitle");

const output = async (url, callback) => {
  const res = await fetch(url);
  const data = await res.json();
  mainData = data;

  callback(data);
};

const populate = (data) => {
  let tableContent = "";
  tableContent += `<tr>${Object.keys(data[0])
    .map((key) => `<th>${key}</th>`)
    .join("")}</tr>`;

  // tableContent += data.map(el => `<tr><th>${el.title}</th><th>${el.year}</th></tr>`).join('')

  tableContent += data
    .map(
      (el) =>
        `<tr>${Object.keys(data[0])
          .map((key) => `<td>${el[key]}</td>`)
          .join("")}</tr>`
    )
    .join("");

  const titles = data.reduce((acc, val) => {
    acc.push(val.title);
    return acc;
  }, []);

  for (let val of titles) {
    let option = document.createElement("option");
    option.setAttribute("value", val);

    let optionText = document.createTextNode(val);
    option.appendChild(optionText);

    movieTitle.appendChild(option);
  }
  console.log(titles);

  table.innerHTML = tableContent;
};

findYear.addEventListener("keyup", function (e) {
  const year = +e.target.value;

  if (year > 2000 && year < 2024) {
    const filteredData = mainData.filter((val) => val.year == year);
    populate(filteredData);
  } else if (year == 0) {
    populate(mainData);
  }
});

movieTitle.addEventListener("change", (e) => {
  let title = e.target.value;
  let data = mainData.filter((val) => val.title === title);
  if (title === "Movie title") {
    populate(mainData);
  } else {
    populate(data);
  }
});

output(url, populate);
