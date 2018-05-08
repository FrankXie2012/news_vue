<template>
<div class="container">
	<div class="header">
		<router-link to="index">News</router-link>
	</div>
	<div class="box">
		<el-card class="box-card">
			<div slot="header"
			    class="clearfix">
				<b>{{post.title}}</b>
				<p class="sub-header">
					<span><i class="el-icon-edit"></i> {{post.username}}</span>
					<span><i class="el-icon-tickets"></i> {{post.commentsCount}}</span>
					<div class="star-div">
						<el-button type="primary"
						    size="mini"
						    icon="el-icon-star-off"
						    @click="starPost">
							<span>{{post.starCount || 0}}</span>
						</el-button>
					</div>
				</p>
			</div>
			<div class="text">
				{{post.body}}
			</div>
		</el-card>

		<h3 class="comments">Comments</h3>
		<el-card class="box-card"
		    v-for="item in comments">
			<div slot="header"
			    class="clearfix">
				<b>{{item.name}}</b>
			</div>
			<div class="text">
				{{item.body}}
			</div>
			<div class="comment-footer">
				<span><i class="el-icon-edit"></i> {{item.email}}</span>
			</div>
		</el-card>
	</div>
</div>
</template>

<script>
export default {
	data() {
		return {
			id: '',
			post: {},
			comments: []
		}
	},
	created() {
		this.id = this.$route.query.id;
		this.post = JSON.parse(localStorage.getItem(this.id));
		this.comments = JSON.parse(localStorage.getItem(`${this.id}-comments`));
	},
	methods: {
		starPost() {
			if (!this.post.starCount) this.post.starCount = 0;
			this.$data.post.starCount++;
			this.$data.post = Object.assign({}, this.$data.post);
		}
	}
}
</script>

<style>
.sub-header {
	clear: both;
	margin-bottom: 0;
	margin-top: 10px;
}

.comments {
	text-align: left;
	margin-top: 40px;
	border-bottom: 1px solid #ccc;
}

.comment-footer {
	border-top: 1px solid #ebeef5;
	margin-top: 20px;
	padding: 10px 0 0;
}
</style>
