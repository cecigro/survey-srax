<template>
	<div class="everything">

		<el-row>
			<el-col :span="24"><h2>Create a new survey</h2></el-col>
		</el-row>

		<el-row>
			<el-col :span="24">
				<div class="box-item">
					Title: <el-input v-model="title" size="small" placeholder="Title of survey"/>
				</div>
			</el-col>
		</el-row>
		
		<el-divider/>

		<div class="page-items box-item" v-for="(page, key) in pages" :key="key">
			<el-row>
				<el-col :span="20">
					<span class="title">Page {{ key + 1 }}</span>
				</el-col>
				<el-col :span="4">
					<el-button
						type="danger"
						size="mini"
						v-if="key > 0"
						@click="deletePage(key)"
						style="float: right;"
						plain>Delete this page</el-button>
				</el-col>
			</el-row>
			<el-row :gutter="20">
				<el-col :span="12">
					Title:
					<el-input v-model="page.title" placeholder="Title of page"/>
				</el-col>
				<el-col :span="12">
					Description:
					<el-input type="textarea" rows="3" v-model="page.description"/>
				</el-col>
			</el-row>

			<div v-for="(question, index) in page.questions" :key="index" class="question-item">

				<el-row>
					<el-col :span="24">
						Question {{ index + 1 }}
						<el-button
						v-if="index > 0"
						type="text"
						icon="el-icon-circle-close"
						style="float: right;"
						@click="deleteQuestion(key, index)"/>
					</el-col>
				</el-row>

				<el-row>
					<el-col :span="24">
						<el-input placeholder="Write your question" v-model="question.headings[0].heading">
							<el-select
								v-model="question.family"
								slot="prepend"
								placeholder="Type of question"
								@change="handleTypeChange(key, index)">
								<el-option label="Single choice" value="single_choice"/>
								<el-option label="Multiple choice" value="multiple_choice"/>	
								<el-option label="Matrix" value="matrix"/>	
								<el-option label="Open ended" value="open_ended"/>	
							</el-select>
						</el-input>
					</el-col>
				</el-row>

				<div class="answer-items" v-if="question.family">
					<el-row>
						<el-col :span="24">
							<span class="subtitle">Choices:</span>
						</el-col>
					</el-row>
					<el-row>
						<el-col :span="24">
							<el-button
								type="text"
								icon="el-icon-circle-plus-outline"
								style="float: right;"
								@click="addChoice(key, index)">
									Add another choice
							</el-button>
						</el-col>
					</el-row>
					<el-row v-for="(choice, ix) in question.answers.choices" :key="ix">
						<el-col :span="24">
							<el-input v-model="choice.text" :placeholder="`Write choice ${choice.position}`">
								<el-button
									v-if="ix > 0"
									icon="el-icon-delete"
									slot="append"
									@click="deleteChoice(key, index, ix)"
								/>
							</el-input>
						</el-col>
					</el-row>
				</div>

				<div class="answer-items" v-if="question.family && question.family === 'matrix'">
					<el-row>
						<el-col :span="24">
							<span class="subtitle">Rows:</span>
						</el-col>
					</el-row>
					<el-row>
						<el-col :span="24">
							<el-button
								type="text"
								icon="el-icon-circle-plus-outline"
								style="float: right;"
								@click="addRow(key, index)">
									Add another row
							</el-button>
						</el-col>
					</el-row>
					<el-row v-for="(row, ix) in question.answers.rows" :key="ix">
						<el-col :span="24">
							<el-input v-model="row.text" :placeholder="`Write row ${ix + 1}`">
								<el-button
									v-if="ix > 0"
									icon="el-icon-delete"
									slot="append"
									@click="deleteRow(key, index, ix)"
								/>
							</el-input>
						</el-col>
					</el-row>
				</div>

				<el-divider v-if="page.questions.length > 1"/>

			</div>

			<el-row style="margin-top: 10px;">
				<el-col :span="24">
					<el-button
							type="primary"
							@click="addQuestion(key)"
							style="float: right;"
							icon="el-icon-circle-plus-outline"
							size="small">Add new question
					</el-button>
				</el-col>
			</el-row>
		</div>

		<el-row>
			<el-col :span="24" style="text-align: center;">
				<el-button type="primary" @click="addPage">Add Page</el-button>
			</el-col>
		</el-row>

	</div>
</template>
<script>
	export default {
		name: 'survey-create',
		data() {
			return {
				title: '',
				pages: [
					{
						title: '',
						description: '',
						position: 1,
						questions: [
							{
								family: null,
								subtype: 'vertical',
								forced_ranking: null,
								answers: {
									choices: [
										{ text: '', position: 1 },
									],
									rows: [],
								},
								headings: [
									{ heading: '', position: 1 },
								],
							},
						],
					}
				],
			}
		},
		methods: {
			addPage() {
				this.pages.push({
					title: '',
					description: '',
					position: 1,
					questions: [
						{
							family: null,
							subtype: 'vertical',
							answers: {
								choices: [
									{ text: '', position: 1 },
								],
								rows: [],
							},
							headings: [
								{ heading: '', position: 1 },
							],
						},
					],
				});
			},
			deletePage(key) {
				this.pages.splice(key, 1);
			},
			addQuestion(key) {
				this.pages[key].questions.push({
					family: null,
					subtype: 'vertical',
					answers: {
						choices: [
							{ text: '', position: 1 },
						],
						rows: [],
					},
					headings: [
						{ heading: '', position: 1 },
					],
				});
			},
			deleteQuestion (key, index) {
				this.pages[key].questions.splice(index, 1);
			},
			addChoice(key, index) {
				const position = this.pages[key].questions[index].answers.choices.length + 1;
				this.pages[key].questions[index].answers.choices.push({ text: '', position: position });
			},
			deleteChoice (key, index, ix) {
				this.pages[key].questions[index].answers.choices.splice(ix, 1);
			},
			handleTypeChange(key, index) {
				const family = this.pages[key].questions[index].family;
				if (family === 'matrix') {
					this.pages[key].questions[index].answers.rows.push({ text: '' });
				}
			},
			addRow(key, index) {
				this.pages[key].questions[index].answers.rows.push({ text: '' });
			},
			deleteRow (key, index, ix) {
				this.pages[key].questions[index].answers.rows.splice(ix, 1);
			},
		}
	}
</script>
<style lang="scss">
	.everything {
		margin: 0 150px;
		.box-item {
			display: block;
			position: relative;
			background: #f6fbff;
			border: 1px solid #bfe3ff;
			border-radius: 7px;
			padding:10px;
			.title{
				display: block;
				font-weight: bold;
				margin-bottom: 10px;
			}
			.question-item {
				.el-row{
					.el-col{
						.el-input {
							.el-input-group__prepend {
								width: 110px;
							}
						}
					}
				}
				.answer-items {
					.el-row {
						.el-col {
							.subtitle {
								display: block;
								position: relative;
								top: 30px;
							}
							.el-input {
								margin-top: 10px;
							}
						}
					}
				}
			}
		}
		.page-items {
			display: block;
			margin: 15px 0;
		}
	}
</style>