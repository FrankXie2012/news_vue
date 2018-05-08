<template>
<div class="container">
	<div class="header">
		<router-link to="index">News</router-link>
	</div>
	<div class="box">
		<el-card class="box-card"
		    v-for="(post, index) in postsNow">
			<div slot="header"
			    class="clearfix">
				<a @click="toDetail(post)">{{post.title}}</a>
				<div class="star-div">
					<el-button type="primary"
					    size="mini"
					    @click="starPost(index)"
					    icon="el-icon-star-off">
						<span>{{post.starCount || 0}}</span>
					</el-button>
				</div>
			</div>
			<div class="text">
				<span><i class="el-icon-edit"></i> {{post.username}}</span>
				<span><i class="el-icon-tickets"></i> {{post.commentsCount}}</span>
			</div>
		</el-card>
	</div>
	<el-pagination layout="prev, pager, next"
	    @current-change="changePage"
	    :page-size="pageSize"
	    :current-page.sync="pageNum"
	    :total="pageTotal">
	</el-pagination>
</div>
</template>

<script>
import axios from 'axios';
export default {
	data() {
		return {
			pageSize: 5,
			pageNum: null,
			pageTotal: null,
			posts: [], // 总新闻列表
			postsNow: [] // 当前页新闻列表
		}
	},
	watch: {
		pageNum(val) {
			this.postsNow = this.posts.slice((this.pageNum - 1) * 5, this.pageNum * 5);
			this.getUserComment(this.postsNow);
		}
	},
	created() {
		this.getPosts();
	},
	methods: {
		async getPosts() {
			const res = await axios.get('http://jsonplaceholder.typicode.com/posts');
			this.posts = res.data;
			this.pageTotal = this.posts.length;
			// 获取第一页数据
			this.postsNow = this.posts.slice((this.pageNum - 1) * 5, this.pageNum * 5);
			this.getUserComment(this.postsNow);
		},
		async getUserComment(val) {
			// 获取当前页用户id，并去重
			const userIds = _.uniqBy(_.map(val, 'userId'), 'userId');
			const userArr = [];
			await Promise.all(userIds.map(async (item, j) => {
				// 获取用户信息
				const userUrl = `http://jsonplaceholder.typicode.com/users/${item}`;
				const res = await axios.get(userUrl);
				userArr.push({
					userId: item,
					username: res.data.username
				});
			}));
			val.forEach(async (post, i) => {
				userArr.forEach((user) => {
					if (post.userId = user.userId) {
						this.$set(post, 'username', user.username);
					}
				});

				// 获取评论数
				const commentUrl = `http://jsonplaceholder.typicode.com/posts/${post.id}/comments`;
				const comRes = await axios.get(commentUrl);
				localStorage.setItem(`${post.id}-comments`, JSON.stringify(comRes.data));
				this.$set(post, 'commentsCount', comRes.data.length);
			});
		},
		starPost(index) {
			const _post = this.$data.postsNow[index];
			if (!_post.starCount) _post.starCount = 0;
			_post.starCount++;
			this.$data.postsNow = Object.assign({}, this.$data.postsNow);
		},
		changePage(val) {
			this.pageNum = val;
		},
		toDetail(val) {
			localStorage.setItem(val.id, JSON.stringify(val));
			this.$router.push({
				path: 'detail',
				query: {
					id: val.id
				}
			});
		}
	}
}
</script>

<style>
a {
	cursor: pointer;
	color: #409eff;
}

.container {
	width: 700px;
	height: 800px;
	overflow: auto;
	margin: 30px auto;
	border: 1px solid #cdcdcd;
}

.box {
	padding: 20px;
}

.box .box-card {
	text-align: left;
	margin-bottom: 15px;
}

.header {
	height: 60px;
	line-height: 60px;
	font-size: 28px;
	border-bottom: 1px solid #cdcdcd;
	text-align: left;
	padding: 0 20px;
}

.star-div {
	float: right;
}

.text span {
	margin-right: 10px;
	color: #999;
}
</style>
