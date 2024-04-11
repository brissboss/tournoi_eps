<script lang="ts" setup>
import { ref, watch } from 'vue'
import Score from '../components/Score.vue';

interface Student {
	id: string,
	order: string,
	name: string,
	points: string,
	match: string,
	arbitre: string,
	trash: boolean
}

const student = ref([] as Student[])

const studentName = ref<string>('')
const selectedStudent1 = ref<string | null>(null)
const selectedStudent2 = ref<string | null>(null)
const selectedStudent3 = ref<string | null>(null)

const dialogResetPoints = ref(false)
const dialogReset = ref(false)
const dialogResumeMatch = ref(false)

const errorMessage = ref('')

if (localStorage.getItem('student') !== null) {
	student.value = JSON.parse(localStorage.getItem('student') as string)
}

function addStudent() {
	student.value.push({
		id: Math.random().toString(36),
		order: (student.value.length + 1).toString(),
		name: studentName.value,
		points: '0',
		match: '0',
		arbitre: '0',
		trash: false
	})
	studentName.value = ''

	localStorage.setItem('student', JSON.stringify(student.value))
}

function deleteStudent(id: string) {
	student.value = student.value.filter(student => student.id !== id)
	localStorage.setItem('student', JSON.stringify(student.value))
}

function findStudentById(id: string) {
	return student.value.find(student => student.id === id)?.name
}

function getPoint() {
	if (selectedStudent1.value === null || selectedStudent2.value === null) {
		return
	}

	const student1 = student.value.find(student => student.id === selectedStudent1.value)
	const student2 = student.value.find(student => student.id === selectedStudent2.value)

	if (student1 === undefined || student2 === undefined) {
		return
	}

	const points1 = parseInt(student1.points)
	const order1 = parseInt(student1.order)
	const order2 = parseInt(student2.order)

	if (points1 === 0 || parseInt(student2.points) === 0)
		return 3;
	else {
		switch (order1 - order2) {
			case 5:
				return 6;
				break
			case 4:
				return 5;
				break
			case 3:
				return 5;
				break
			case 2:
				return 5;
				break
			case 1:
				return 4;
				break
			case -1:
				return 4;
				break
			case -2:
				return 3;
				break
			case -3:
				return 3;
				break
			case -4:
				return 3;
				break
			case -5:
				return 2;
				break
			default:
				break
		}
	}

	return 0;
}

function calculateOrder(id: string, points: string, id2: string, points2: string) {
	const studentsCopy = JSON.parse(JSON.stringify(student.value))

	const studentCopy = studentsCopy.find(student => student.id === id)
	const studentCopy2 = studentsCopy.find(student => student.id === id2)

	if (studentCopy === undefined || studentCopy2 === undefined)
		return
	studentCopy.points = (parseInt(studentCopy.points) + points).toString()
	studentCopy2.points = (parseInt(studentCopy2.points) + points2).toString()

	studentsCopy.sort((a, b) => parseInt(b.points) - parseInt(a.points))

	const order = studentsCopy.findIndex(student => student.id === id) + 1

	if (order === 1) return '1er'
	else return order + 'ème'
}

function calculatePoints() {
	if (selectedStudent1.value === null || selectedStudent2.value === null)
		return

	const student1 = student.value.find(student => student.id === selectedStudent1.value)
	const student2 = student.value.find(student => student.id === selectedStudent2.value)

	if (student1 === undefined || student2 === undefined)
		return

	const points1 = parseInt(student1.points)
	const order1 = parseInt(student1.order)
	const points2 = parseInt(student2.points)
	const order2 = parseInt(student2.order)

	if (points1 === 0 || points2 === 0)
		student1.points = (points1 + 3).toString()
	else {
		switch (order1 - order2) {
			case 5:
				student1.points = (points1 + 6).toString()
				break
			case 4:
				student1.points = (points1 + 5).toString()
				break
			case 3:
				student1.points = (points1 + 5).toString()
				break
			case 2:
				student1.points = (points1 + 5).toString()
				break
			case 1:
				student1.points = (points1 + 4).toString()
				break
			case -1:
				student1.points = (points2 + 4).toString()
				break
			case -2:
				student1.points = (points2 + 3).toString()
				break
			case -3:
				student1.points = (points2 + 3).toString()
				break
			case -4:
				student1.points = (points2 + 3).toString()
				break
			case -5:
				student1.points = (points2 + 2).toString()
				break
			default:
				break
		}
	}

	student2.points = (points2 + 1).toString()

	let student3 = student.value.find(student => student.id === selectedStudent3.value)
	if (student3 === undefined)
		return
	student3.points = (parseInt(student3.points) + 1).toString()

	student1.match = (parseInt(student1.match) + 1).toString()
	student2.match = (parseInt(student2.match) + 1).toString()
	student3.arbitre = (parseInt(student3.arbitre) + 1).toString()

	student.value = student.value.sort((a, b) => parseInt(b.points) - parseInt(a.points)).map((student, index) => {
		student.order = (index + 1).toString()
		return student
	})

	localStorage.setItem('student', JSON.stringify(student.value))

	selectedStudent1.value = null
	selectedStudent2.value = null
	selectedStudent3.value = null

	dialogResumeMatch.value = false
}

function resetPoints() {
	student.value = student.value.map(student => {
		student.points = '0'
		student.match = '0'
		student.arbitre = '0'
		return student
	})

	localStorage.setItem('student', JSON.stringify(student.value))
	dialogResetPoints.value = false
}

function reset() {
	student.value = []
	localStorage.removeItem('student')
	dialogReset.value = false
}

function getOrder(id: string) {
	return Number(student.value.find(student => student.id === id)?.order)
}

function openValidPopup() {
	if (selectedStudent1.value === null || selectedStudent2.value === null || selectedStudent3.value === null) {
		errorMessage.value = 'Veuillez sélectionner deux élèves et un arbitre'
		return
	}

	if (selectedStudent1.value === selectedStudent2.value) {
		errorMessage.value = 'Les élèves sélectionnés sont identiques'
		return
	}

	if ((selectedStudent1.value === selectedStudent3.value) || (selectedStudent2.value === selectedStudent3.value)) {
		errorMessage.value = 'L\'arbitre ne peut pas être un des élèves'
		return
	}

	if (Number(student.value.find(student => student.id === selectedStudent1.value)?.points) !== 0 && Number(student.value.find(student => student.id === selectedStudent2.value)?.points) !== 0) {
		if (getOrder(selectedStudent1.value) - getOrder(selectedStudent2.value) > 5 || getOrder(selectedStudent1.value) - getOrder(selectedStudent2.value) < -5) {
			errorMessage.value = 'Les élèves sélectionnés sont trop éloignés dans le classement (5 places maximum)'
			return
		}
	}

	dialogResumeMatch.value = true
}

watch(() => selectedStudent1.value, () => {
	errorMessage.value = ''
})

watch(() => selectedStudent2.value, () => {
	errorMessage.value = ''
})

function getOrderNumber(order: string) {
	if (order === '1') return '🥇'
	else if (order === '2') return '🥈'
	else if (order === '3') return '🥉'
	else return order
}
</script>

<template>
	<div
		style="
			display: flex;
			flex-direction: row;
		"
	>
		<v-col cols="6"
			style="
				border-right: 0.5px solid gray;
				padding: 0;
				margin: 0;
				max-height: 100dvh;
				overflow-y: auto;
			"
		>
			<VDataTable
				style="
					height: 100dvh;
					overflow-y: auto;
				"
				:headers="[
					{ title: 'Ordre', value: 'order', sortable: false, align: 'center'},
					{ title: 'Élèves', value: 'name', sortable: false, align: 'center'},
					{ title: 'Points', value: 'points', sortable: false, align: 'center' },
					{ title: 'Matchs', value: 'match', sortable: false, align: 'center' },
					{ title: 'Arbitrages', value: 'arbitre', sortable: false, align: 'center' },
					{ title: 'Actions', value: 'action', sortable: false, align: 'center' }
				]"
				:items="student.sort((a, b) => parseInt(b.points) - parseInt(a.points))"

				fixed-header
				:items-per-page="-1"

				:no-data-text="'Aucun élève n\'a encore été ajouté'"
				density="compact"
			>

				<template #item.order="{ item }">
					<div
						style="
							font-size: 1.3rem;
						"
					>
						{{ getOrderNumber(item.order) }}
					</div>
				</template>

				<template #item.action="{ item }">
					<v-dialog
						v-model="item.trash"
						max-width="400"
					>
						<template #activator="{ props: activatorProps }">
							<v-icon
								@click="item.trash = true"
								v-bind="activatorProps"
							>
								mdi-delete
							</v-icon>
						</template>

						<v-card>
							<v-card-title>
								Supprimer l'élève
							</v-card-title>
							<v-card-text>
								<p>
									Voulez-vous vraiment supprimer l'élève <b>{{ item.name }}</b> ?
								</p>
							</v-card-text>
							<v-card-actions
								style="
									display: flex;
									justify-content: end;
								"
							>
								<v-btn
									@click="item.trash = false"
								>
									Annuler
								</v-btn>
								<v-btn
									@click="deleteStudent(item.id)"
									color="error"
									variant="filled"
								>
									Supprimer
								</v-btn>
							</v-card-actions>
						</v-card>
					</v-dialog>
				</template>

				<template #bottom/>
			</VDataTable>
		</v-col>
		<v-col cols="6"
			style="
				border-right: 0.5px solid gray;
				padding-top: 0;
				padding-bottom: 0;
				margin: 0;
				max-height: 100dvh;
				overflow-y: auto;
			"
		>
			<div
				style="
					display: flex;
					flex-direction: column;
					align-items: center;
					height: 100dvh;
					width: 100%;
				"
			>
					<v-card
						style="
							width: 100%;
							margin-top: 20px;
							margin-bottom: 20px;
							background-color: #efefef;
						"
					>
						<v-card-title>
							Résultats de match
						</v-card-title>
						<v-card-text>
							<div
								style="
									display: flex;
									flex-direction: row;
									align-items: center;
								"
							>
								<v-select
									:items="student"
									name="student1"
									label="Élève 1"
									v-model="selectedStudent1"
									:item-title="'name'"
									:item-value="'id'"

									density="compact"
									variant="outlined"
									style="margin-right: 15px; width: 38%;"

									:hide-details="errorMessage !== '' ? false : true"
									:error-messages="errorMessage"
								/>
								<p style="width: 23%; text-align: center;">A gagner contre</p>
								<v-select
									:items="student"
									name="student2"
									label="Élève 2"
									v-model="selectedStudent2"
									:item-title="'name'"
									:item-value="'id'"

									density="compact"
									variant="outlined"
									style="margin-left: 15px; width: 38%"

									:hide-details="errorMessage !== '' ? false : true"
									:error-messages="errorMessage"
								/>
							</div>
							<div
								style="
									display: flex;
									margin-top: 20px;
								"
							>
								<v-select
									:items="student"
									name="student3"
									label="Arbitre"
									v-model="selectedStudent3"
									:item-title="'name'"
									:item-value="'id'"

									density="compact"
 									variant="outlined"

									:hide-details="errorMessage !== '' ? false : true"
									:error-messages="errorMessage"
								/>
							</div>
							<div
								style="
									display: flex;
									align-items: center;
									justify-content: end;
								"
							>
								<v-dialog
									v-model="dialogResetPoints"
									max-width="400"
								>
									<template #activator="{ props: activatorProps }">
										<v-btn
											@click="dialogResetPoints = true"
											color="error"
											variant="text"
											style="margin-top: 20px"
											v-bind="activatorProps"
										>
											Réinitialiser les points
										</v-btn>
									</template>

									<v-card>
										<v-card-title>
											Réinitialiser les points
										</v-card-title>
										<v-card-text>
											<p>
												Voulez-vous vraiment réinitialiser les points de tous les élèves ?
											</p>
										</v-card-text>
										<v-card-actions
											style="
												display: flex;
												justify-content: end;
											"
										>
											<v-btn
												@click="dialogResetPoints = false"
											>
												Annuler
											</v-btn>
											<v-btn
												@click="resetPoints"
												color="error"
												variant="filled"
											>
												Réinitialiser
											</v-btn>
										</v-card-actions>
									</v-card>
								</v-dialog>
								<!-- <v-btn
									color="error"
									style="margin-top: 20px; margin-right: 10px;"
									variant="text"
									@click="resetPoints"
								>
									Réinitialiser
								</v-btn> -->
								<v-dialog
									v-model="dialogResumeMatch"
									max-width="400"
								>
									<template #activator>
										<v-btn
											@click="openValidPopup"
											color="primary"
											variant="outlined"
											style="margin-top: 20px; width: 30%; margin-left: 10px"
										>
											Valider
										</v-btn>
									</template>

									<v-card>
										<v-card-title>
											Valider le match
										</v-card-title>

										<v-card-text>
											<v-row>
												<v-col
													cols="6"
													style="
														display: flex;
														flex-direction: column;
														justify-content: center;
														align-items: center;
														border-right: 1px solid #efefef;
													"
												>
													<p style="font-size: 1.5rem;">
														Gagné
													</p>
													<p style="font-size: 2rem;">
														<b class="text-success mr-1">{{ findStudentById(selectedStudent1) }}</b>
													</p>
													<p
														style="
															margin-top: 10px;
															font-size: 0.8rem;
															align-self: flex-start;
															color: #5f5f5f;
														"
													>
														- <b>{{ getPoint() }}</b> points gagnés
													</p>
													<p
														style="
															font-size: 0.8rem;
															align-self: flex-start;
															color: #5f5f5f;
														"
													>
														- <b>{{ calculateOrder(selectedStudent1, getPoint(), selectedStudent2, 1) }}</b> au classement
													</p>
												</v-col>
												<v-col
													cols="6"
													style="
														display: flex;
														flex-direction: column;
														justify-content: center;
														align-items: center;
													"
												>
													<p style="font-size: 1.5rem;">
														Perdu
													</p>
													<p style="font-size: 2rem;">
														<b class="text-error mr-1">{{ findStudentById(selectedStudent2) }}</b>
													</p>
													<p
														style="
															margin-top: 10px;
															font-size: 0.8rem;
															align-self: flex-start;
															color: #5f5f5f;
														"
													>
														- <b>1</b> point gagné
													</p>
													<p
														style="
															font-size: 0.8rem;
															align-self: flex-start;
															color: #5f5f5f;
														"
													>
														- <b>{{ calculateOrder(selectedStudent2, 1, selectedStudent1, getPoint()) }}</b> au classement
													</p>
												</v-col>
											</v-row>
										</v-card-text>

										<v-card-actions
											style="
												display: flex;
												justify-content: end;
											"
										>
											<v-btn
												@click="dialogResumeMatch = false"
												color="error"
											>
												Annuler
											</v-btn>
											<v-btn
												@click="calculatePoints"
												color="primary"
												variant="filled"
											>
												Valider
											</v-btn>
										</v-card-actions>
									</v-card>

								</v-dialog>
							</div>
						</v-card-text>
					</v-card>
					<v-card
						style="
							width: 100%;
							margin-bottom: 20px;
							background-color: #efefef;
						"
					>
						<v-card-title>
							Ajouter un élève
						</v-card-title>
						<v-card-text
							style="
								display: flex;
								flex-direction: column;
								align-items: end;
								justify-content: center;
							"
						>
							<v-text-field
								v-model="studentName"
								label="Nom de l'élève"
								variant="outlined"
								density="comfortable"
								hide-details
								style="width: 100%;"
							/>
							<div
								style="
									display: flex;
									flex-direction: row;
									justify-content: end;
									width: 100%;
								"
							>
								<v-dialog
									v-model="dialogReset"
									max-width="400"
								>
									<template #activator="{ props: activatorProps }">
										<v-btn
											@click="dialogReset = true"
											color="error"
											variant="text"
											style="margin-top: 20px"
											v-bind="activatorProps"
										>
											Réinitialiser
										</v-btn>
									</template>

									<v-card>
										<v-card-title>
											Réinitialiser les élèves
										</v-card-title>
										<v-card-text>
											<p>
												Voulez-vous vraiment supprimer tous les élèves ?
											</p>
										</v-card-text>
										<v-card-actions
											style="
												display: flex;
												justify-content: end;
											"
										>
											<v-btn
												@click="dialogReset = false"
											>
												Annuler
											</v-btn>
											<v-btn
												@click="reset"
												color="error"
												variant="filled"
											>
												Réinitialiser
											</v-btn>
										</v-card-actions>
									</v-card>
								</v-dialog>
								<v-btn
									@click="addStudent"
									color="primary"
									variant="outlined"
									style="margin-top: 20px;"
								>
									Ajouter
								</v-btn>
							</div>
						</v-card-text>
					</v-card>
					<Score />
			</div>
		</v-col>
	</div>
</template>
