---
layout: post
title: Location-based Preference-Aware Recommendation Using Sparse Geo-Social Networking
  Data 笔记
category: "理论及算法"
tag: "LBSN及推荐"
---
<p>此文章的推荐考虑了1用户个人偏好和2社会意见。该推荐系统包括两个方面：离线建模和在线推荐。在离线建模模型中系统使用weighted category hierarchy(WCH)对用户的访问历史进行建模，并且针对每个城市的每个类型(category)的位置推断出每个用户的专家值(expertise)。在在线推荐模块，首先通过偏好感知的候选选择算法找到本地的专家和此用户当前位置区域的item，然后根据本地专家的意见计算打分并获得最终评价。
</p><p>稀疏性是推荐的一个很大的困难，尤其到了一个新的城市，用户的历史轨迹可能非常少，对推荐造成了更大的困难。为了解决这种问题，文中通过位置历史的类型对用户偏好建模，这样可以利用在地理空间和用户没有关系的其他用户的历史信息。
</p><h1>1 想法
</h1><p>本文数据上使用的用户是来自一个城市的，但是在算法中并没有考虑用户的地域性，<span style="background-color:yellow">文中也提及使用所有城市的数据建模</span>。而在另一篇文章中说明，不同地域的人偏好不同。是否可以同时考虑本地的专家和外地的专家，本地专家（即生活在本地）对本地更了解，而外地的专家（与用户来自于同一城市，来旅游等短暂停留，本文中没有考虑，但是数据中使用的就是外地的专家）和用户的偏好可能更相同。
</p><p>本文主要是解决了稀疏性的问题。
</p><p>同一个城市不同地区可能有不同的专家，可以在专家发现时引入位置的层次模型。
</p><h1>2 系统介绍
</h1><p>下图介绍了系统中的重要的数据结构以及他们之间的关系，包括用户、地点、签到、用户位置历史、位置的层次结构类型(category hierarchy)，如图所示。<img src="http://www.codingart.info/wp-content/uploads/2012/10/102512_0305_Locationbas1.png" alt=""/>
	</p><p>系统架构如下图
</p><p style="text-align: center"><img src="http://www.codingart.info/wp-content/uploads/2012/10/102512_0305_Locationbas2.png" alt=""/>
	</p><h1>3离线建模(图中左下)
</h1><p>分为两个模块，社会知识挖掘(social knowledge learning)和个人偏好发现(personal preference discovery)。
</p><h2>3.1社会知识挖掘
</h2><p>该模块针对每一个城市中的每一个位置类型推断用户的专家值expertise。首先给定层次类型，然后将每个城市的所有位置历史根据类型将其分组(每个历史可能在不同的父子类型中)，然后使用HITS算法计算用户的专家值，然后就能找到城市中的不同类型的本地专家。
</p><h2>3.2个人偏好发现
</h2><p>该模块使用根据用户历史访问位置的类型使用WCH（weighted category hierarchy）对其建模，每个节点的值为用户在此类型的位置中访问的次数，使用TF-IDF表示。
</p><h1>4在线推荐（图中右上）
</h1><p>推荐考虑用户的偏好、当前位置和社会意见，分为两个模块，偏好感知的候选者选取和基于位置的打分。
</p><h2>4.1偏好感知的候选者选取
</h2><p>该模块根据用户偏好选择一组在访问过此区域的本地专家。
</p><h2>4.2基于位置的打分
</h2><p>该模块首先计算本地专家和该用户的相似度，然后根据相似度和本地专家的访问历史对候选位置进行打分，然后生成推荐。
</p><h1>5 实验
</h1><p>实验使用的数据集是Foursquare的数据，分别抓取了在纽约和洛杉矶的签到数据，同时抓取了这些用的其他城市的数据用来做偏好挖掘。同时只考虑带有评论的签到，这样防止用户仅仅是在门口路过。
</p><p>文章为了更好的模拟用户在一个不熟悉的城市使用推荐系统的效果，使用家乡为New Jersey的用户在洛杉矶和纽约的签到数据（数据中所有的用户来自于同一个城市，更倾向于存在相似的偏好）。
</p><p>同时，使用以下四种方法用来和我们的方法进行比较：（论文中只使用了小篇幅介绍算法，不太详细，不知道我的理解对不对）
</p><ul><li>Most-Preferred-Category-based(MPC)：我的理解是根据用户当前的地理范围和用户偏好WCH，找到每个偏好中最好(authority最高)的位置。<span style="background-color:yellow">（但是我感觉和下表不同，考虑了社会意见，因为authority高的说明其他用户去得也多）</span>
		</li><li>Location-based Collaborative Filtering(LCF)：根据用户在一个城市的轨迹，构建用户-位置矩阵，使用传统的协同过滤方法对用户选择区域内的位置打分。
</li><li>Preference-based Collaborative Filtering(PCF)：首先检索在用户选择的区域内的所有用户和位置，并构建用户-位置矩阵，然后使用user-based协同过滤模型预测用户对未知的打分。用户之间的相似度使用类型向量(category vectors)进行计算。
</li><li>Our method without using 偏好感知的候选者搜索。
</li></ul><p style="text-align: center"><img src="http://www.codingart.info/wp-content/uploads/2012/10/102512_0305_Locationbas3.png" alt=""/>
	</p><p>准确性（effectiveness）评价方法：将一个用户的历史位置分为两份，一份用来做训练集，一份用来做测试集，根据训练集获得推荐结果然后和测试集比较，使用精度和召回率评价：
</p><p><img src="http://www.codingart.info/wp-content/uploads/2012/10/102512_0305_Locationbas4.png" alt=""/>
	</p><p>虽然这个方法不准确，因为用户没去过一个地方，也可能对这个地方感兴趣，或者用户访问过一个地方但是没有留评论，但是在实验结果中可以看出，本文的方法比其他方法更好。
</p><p>精度和召回率和以下三个因素有关：<span style="background-color:yellow">请求推荐的数量N、用户历史轨迹数量和用户的请求区域中签到的位置的密度。</span>
	</p><p>性能评价：<span style="background-color:yellow">在线推荐的性能和两方面有关：用户选择的地理范围和用户要求的推荐数量。</span>
	</p><p>在评价准确性时，文章分别针对不同的推荐数量、用户历史位置数量和位置密度对四种方法做了比较，针对不同的推荐数量还将本文提出的算法和不考虑偏好感知候选搜索的算法比较。通过比较证明，文章提出的算法考虑的四个部分都对推荐有一定的作用。同时还根据用户相似度计算是否考虑层次和嫡，证明文中提出的用户相似度公式较好。
</p><p>实现发现纽约的数据更多，但效果更差，分析并统计说明数据多说明人去得多，偏好可能会更加分散。
</p><p><span style="background-color:aqua">实验设置：要根据自己的系统中的各个组件进行实验，然后证明各个组件对结果有积极的作用。对于和尝试不太一样的地方，要分析其原因并尽量通过数据证明。</span>
	</p><p>在评价性能是，文章问对不同的推荐数量和选择的空间区域范围进行比较。同时，还比较了最后一步中涉及到的用户和位置的数量，来说明基于偏好的候选集选择算法的效果：去掉了经验少的用户和质量差的位置。
</p><p>
?</p><p>
?</p><p>
?</p><p>
?</p>
