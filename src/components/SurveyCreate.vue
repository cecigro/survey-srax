<template>
	<div>
		<div style="margin-left: 50px;">Create a new survey</div>
		<div v-for="(item, key) in items" :key="key" class="question-item">
			<span>
				Question {{ key + 1 }}
				<el-button
					v-if="key > 0"
					type="text"
					icon="el-icon-circle-close"
					style="float: right;"
					@click="deleteQuestion(key)">
				</el-button>
			</span>
			<el-input placeholder="Write your question" v-model="item.question" size="mini">
				<el-select v-model="item.type" slot="prepend" placeholder="Type of question" @change="handleTypeChange(key)">
					<el-option label="Text" :value="1"/>
					<el-option label="Multiple choice" :value="2"/>
					<el-option label="Checkboxes" :value="3"/>
					<el-option label="Dropdown" :value="4"/>
				</el-select>
			</el-input>
			<div v-if="item.options.length" class="option-items">
				<el-divider></el-divider>
				<span>Options:</span>
				<el-button
					type="text"
					icon="el-icon-circle-plus-outline"
					style="float: right;"
					@click="addOptions(key)"
					size="mini">
					Add Option
				</el-button>
				<br>
				<el-input
					size="mini"
					v-for="(op, ix) in item.options"
					:key="ix"
					v-model="op.option"
					:placeholder="`Option ${ ix + 1 }`">
					<el-button icon="el-icon-delete" slot="append" @click="deleteOption(key,ix)"></el-button>
				</el-input>
			</div>
		</div>
		<el-button
				type="primary"
				@click="addItem"
				style="float: right; margin-right: 50px;"
				icon="el-icon-circle-plus-outline"
				size="mini">Add new question</el-button>
	</div>
</template>
<script>
	export default {
		name: 'survey-create',
		data() {
			return {
				items: [
					{ question: '', type: '', options: []}
				],
			}
		},
		methods: {
			addItem() {
				this.items.push({ question: '', type: '', options: []});
			},
			deleteQuestion(key) {
				this.items.splice(key, 1);
			},
			handleTypeChange(key) {
				this.items[key].options = [];
				if (this.items[key].type !== 1) {
					this.items[key].options.push({option: ''});
				}
			},
			addOptions(index) {
				this.items[index].options.push({option: ''});
			},
			deleteOption(index, ix) {
				this.items[index].options.splice(ix, 1);
			}
		}
	}
</script>
<style lang="scss">
	.question-item {
		display: block;
    position: relative;
    background: #f6fbff;
    margin: 10px 50px;
    border: 1px solid #bfe3ff;
    border-radius: 7px;
    padding:10px;
		>span {
			display: block;
			position: relative;
		}
		>.el-input {
			.el-input-group__prepend {
				width: 100px;
			}
		}
		.option-items {
			>.el-input {
				margin: 5px 0;
			}
		}
	}
</style>