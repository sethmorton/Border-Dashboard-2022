<script lang="ts">
	import { lineChart } from '$lib/helpers/lineChart';
	import { onMount } from 'svelte';
	import { Helper } from '$lib/helpers/btsHelper';
	import { Datepicker } from 'svelte-calendar';
	import { detach } from 'svelte/internal';
	/**
	 * Although I would love to stick with my capitalized constant naming convention, the npm package requires this naming
	 */
	const theme = {
		calendar: {
			width: '30vw',
			maxWidth: '100vw',
			legend: {
				height: '45px'
			},
			shadow: '0px 10px 26px rgba(0, 0, 0, 0.25)',
			colors: {
				text: {
					primary: '#333',
					highlight: '#fff'
				},
				background: {
					primary: '#fff',
					highlight: '#eb7400',
					hover: '#eee'
				},
				border: '#eee'
			},
			font: {
				regular: '1.5em',
				large: '10em'
			},
			grid: {
				disabledOpacity: '.35',
				outsiderOpacity: '.6'
			}
		}
	};
	const PASSENGERS = ['Personal Vehicle Passengers', 'Train Passengers', 'Bus Passengers'];
	const VEHICLES = ['Personal Vehicle', 'Buses', 'Trains'];
	const TITLES = ["Crossing of Goods", "Crossing of People", "Wait Times"];
	const COLORS = ["bg-purple", "bg-green", "bg-blue"];
	onMount(async () => {
		
		defaultPassengersNumbers = await getCrossingsObject(PASSENGERS, pastDateFormatted, currentDateFormatted, 'San Ysidro');
			console.log(defaultPassengersNumbers);
		// new lineChart('chartItem');
	});
	/**
	 
	 * @property Possible Measures - ["Pedestrians", "Trains", "Buses", "Personal Vehicle Passengers", "Personal Vehicles", "Trucks", "Train Passengers", "Bus Passengers"]
	 * @param measures - Input an array of measures for BTS -  Eg. ["Pedestrians", "Trains", etc]
	 * @param startDate  Date Format - "Year-Month-Day", Eg. "2019-01-01"
	 * @param endDate  Date Format - "Year-Month-Day", Eg. "2019-01-01"
	 * @param port_name Port of Entry, Eg. "San Ysidro"
	 * @returns Returns the sum of each measure for the given time period in an object based on the measures inputted, Eg. {"Pedestrians" : 20000, "Train Passengers" : 5, etc...}
	 */
	async function getCrossingsObject(
		measures: string[],
		startDate: string,
		endDate: string,
		port_name?: string
	) {
		let instance = new Helper(startDate, endDate, port_name);
		let calculatedCrossingsObject = await instance.calculateCrossings(measures);
		return calculatedCrossingsObject;
	}
	const currentDate = Helper.getCurrentDate();
	/**
	 * These dates are for the Svelte Calendar start and end generation
	*/
	const currentDateObject = new Date(currentDate.year, currentDate.month - 1, currentDate.day);
	const previousDateObject = new Date(currentDate.year - 1, currentDate.month - 1, currentDate.day);
	console.log(previousDateObject);
	/**
	 * This is the current date
	 */
	let currentDateFormatted = Helper.dateFormatGenerator(
		currentDate.year,
		currentDate.month,
		currentDate.day
	);
	/**
	 * This is the date a year ago relative to the current date
	 */
	let pastDateFormatted = Helper.dateFormatGenerator(
		currentDate.year - 1,
		currentDate.month,
		currentDate.day
	);

	async function getPedestrianValue() {
		return await getCrossingsObject(['Pedestrian'], pastDateFormatted, currentDateFormatted, 'San Ysidro');
	}
	async function fetchData() {
		const res = await fetch('./controller.json');
		const { rows } = await res.json();

		if (res.ok) {
			console.log(rows);
			return rows;
		} else {
			throw new Error(rows);
		}
	}
</script>

<nav class="navbar navbar-light bg-light">
	<div class="container">
		<a class="navbar-brand" href="#">
			<img
				src="/images/smartborder.png"
				width="30"
				height="30"
				class="d-inline-block align-top me-3"
				alt=""
			/>
			Smart Border Coalition
		</a>
		<button class="btn me-0 btn-primary"> Border Wait Time Graph </button>
	</div>
</nav>
<div class="container mt-3" style="height: 75vh; ">
	<div class="row d-flex justify-content-start" style="height: 75vh;">
		{#each TITLES as title, i}
		<div class="col-lg-4" style="">
			<div class="card" style="height: 75vh;">
				<div class="card-header text-center {COLORS[i]}">
					<h1 class="text-white">{title}</h1>
				</div>
				<div class="card-body">
					<div class="container-fluid">
						<div class="row text-center">
							<div class="col">
								<Datepicker {theme} selected={previousDateObject} end={currentDateObject}/>
								<!-- <h4 class="p-2 fs-4">to</h4> -->
								{#await getPedestrianValue()}
							</div>
							<div class="col">
	
								<h4 class="p-2 fs-4">to</h4>
	
							</div>
							<div class="col">
								<!-- <h4 class="p-2 fs-4">to</h4> -->
								<Datepicker {theme} selected={currentDateObject}/>
							</div>
						</div>
					</div>
					<div class="container-fluid">
						<div class="row align-items-center">
							<div class="col">
								<h3 class="card-title text-bold pt-3 mb-0">Number</h3>
								<span class="card-title pt-0">Pedestrians</span>
							</div>
							<div class="col d-flex justify-content-end">
								<i class="fa fa-arrow-up float-right fa-2xl " style="color: green;" aria-hidden="true"></i>
							</div>
						</div>
					</div>

					<!-- svelte-ignore a11y-invalid-attribute -->
					<a href="" class="btn btn-primary">Go somewhere</a>
				</div>
				<div class="card-footer text-muted">2 days ago</div>
			</div>
		</div>
		{/each}
		<!-- <div class="col container-md">
			<div class="card text-center" style="height: 75vh;">
				<div class="card-header bg-success  ">
					<h1 class="text-center text-white">Crossings of Goods</h1>
				</div>
				<div class="card-body">
					npm i -D svelte-calendar
					<h5 class="card-title">Special title treatment</h5>
					<p class="card-text ">
						With supporting text below as a natural lead-in to additional content.
					</p>
					<a href="#" class="btn btn-primary">Go somewhere</a>
				</div>
				<div class="card-footer text-muted">2 days ago</div>
			</div>
		</div>
		<div class="col container-md">
			<div class="card text-center" style="height: 75vh;">
				<div class="card-header bg-dark"><h1 class="text-white">Wait Times</h1></div>
				<div class="card-body">
					<h5 class="card-title">Special title treatment</h5>
					<p class="card-text">
						With supporting text below as a natural lead-in to additional content.
					</p>
					<a href="#" class="btn btn-primary">Go somewhere</a>
				</div>
				<div class="card-footer text-muted">2 days ago</div>
			</div>
		</div> -->
	</div>
</div>

<div class="navbar fixed-bottom bg-light text-center">
	<div class="container-fluid justify-content-center">
		<span class="text-muted">2022, Smart Border Coalition</span>
	</div>
</div>

<style>
	@import url('https://cdn.jsdelivr.net/npm/bootstrap@5.0.2/dist/css/bootstrap.min.css');
	@import url('https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.1.1/css/all.min.css');
	.bg-purple {
		background-color: purple;
	}
	.bg-green {
		background-color: green;
	}
	.bg-blue {
		background-color: blue;
	}
</style>
