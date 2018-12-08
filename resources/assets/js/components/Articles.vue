<template>
<div>
	<h2>{{msg.create}} AN ARTICLE</h2>
	<form class="mb-2">
		<div class="form-group">
			<label>Title</label>
			<input type="text" class="form-control" v-model="article.title">
		</div>
		<div class="form-group">
			<label>Content</label>
			<textarea class="form-control" v-model="article.body"></textarea>
		</div>
		<button type="submit" class="btn btn-primary" v-on:click.prevent="addArticle">Submit</button>

	</form>
	<hr />
	<h2>ARTICLES</h2>
	<nav aria-label="Page navigation example">
		  <ul class="pagination">
		    <li class="page-item" v-bind:class="{active: pagination.current_page !== 1, disabled: pagination.current_page === 1 }"><a class="page-link" v-on:click="fetchArticles(pagination.prev)">Previous</a></li>
		    <li class="page-item" v-for="number in pagination.numberPages" v-bind:class="{active: pagination.current_page === number }"><a class="page-link" v-on:click="fetchArticles(pagination.path+'?page='+number)"> {{number}}</a></li>
		    <li class="page-item" v-bind:class="{active: pagination.current_page !== pagination.numberPages, disabled: pagination.current_page === pagination.numberPages }"><a class="page-link" v-on:click="fetchArticles(pagination.next)">Next page</a></li>
		  </ul>
	</nav>

	
	<ul class="list-group">
		<li v-for="article in articles" class="list-group-item">

			<h3>{{article.title}}</h3>
			<p> {{article.body}} </p>
			<div><button class="btn btn-primary" @click="editArticle(article)">Edit</button>
			<button class="btn btn-danger" @click="deleteArticle(article)">Delete</button></div>
			
		</li>
	</ul>
	
</div>
</template>

<script>
	export default {
		data() {
			return {
				articles:[],
				article:{
					id:'',
					title:'',
					body:''
				},
				pagination:{
					next:'',
					prev:'',
					path:'',
					current_page:'',
					numberPages:''
				},
				edit:false,
				msg:{create:'CREATE', delete:'DELETE'}
			}
		},
		created(){
			this.fetchArticles();
		},	
		methods:{
			fetchArticles(page_url) {
				page_url = page_url || 'api/articles';
				
			
				fetch(page_url)
					.then(res=>res.json())
					.then(res=>{
						this.pagination= res.links;
						this.articles = res.data;
						this.pagination.numberPages = res.meta.last_page;
						this.pagination.path = res.meta.path;
						this.pagination.current_page= res.meta.current_page;
						
					})
			},
			deleteArticle(article) {
				if (confirm('Are you sure ?')) {

					
					fetch(`api/article/${id}`,{
						method:'DELETE'
					}).then(res=>res.json())
					.then(data=>{
					alert('Article removed !');
					let page = this.pagination.current_page;

					this.fetchArticles('api/articles?page='+this.pagination.current_page);})
					.catch(err=>console.log(err))
				
				}
				

			},
			editArticle(article) {
				this.article = article;
			
			},
			addArticle() {
				// si article_id est renseigné alors c'est un update
				
				let path='api/article';
				let method = this.article.id ? 'PUT':'POST'; 
				
				if(this.article.id)
				{
					this.article.article_id = this.article.id;
					
				}
					
				let data=JSON.stringify(this.article);
				console.log(data);

				
				fetch(path,{method:method,
							body:data,
							headers: {
								'content-type': 'application/json'
								}
							})
					.then(res=>res.json())
					.then(res=>{
						
						alert('Article was created/Updated');
						this.fetchArticles();
					})
					.catch(err=>console.log(err))

				

				

				this.article ={};
				
				// sinon  c'est un nouvel élément
			}
		}
	}
</script>