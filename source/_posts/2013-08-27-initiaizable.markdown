---
layout: post
title: "Initiaizable"
date: 2013-08-27 00:01
comments: true
categories: Rails Source
---
>我的理解是：有序的对类进行初始化。参考*[TSort][]*。
  [TSort]: http://lanceyb.github.io/blog/2013/08/15/tsort
    
<br />
来看个例子
{% codeblock lang:ruby %}

	require "rails"
	class Tour
		include Rails::Initializable

		attr_reader :cities
		
		def initialize
		  @cities = []
		end
	end
	
	###一开始的计划是：先去USA，再去Britain，最后去France，
	Tour.initializer("USA") do |do_the_same_th|
		@cities << { country: "USA", city: "Silicon Valley", do: do_the_some_th }
	end
	
	Tour.initializer("France", before: "Britain") do |do_the_same_th|
	    @cities << { country: "France", city: "Paris", do: do_the_same_th }
	end
	
	Tour.initializer("Britain", before: "USA") do |do_the_same_th|
	    @cities << { country: "Britain", city: "London", do: do_the_same_th }
	end
	
	###后来我改变注意了，想去英国后，先回趟中国，再去法国
	Tour.initializer("China", before: "Britain", after: "France") do |do_the_same_th|
	    @cities << { country: "China", city: "Beijin", do: do_the_same_th }
	end
	
	my_tour = Tour.new
	my_tour.run_initializers(:default, "take a photo")
	
	my_tour.cities.each_with_index do |tour, index|
	  p "#{index}: Went to #{tour[:country]}-#{tour[:city]} and #{tour[:do_the_same_th]}"
	end
{% endcodeblock %}
最后的输出结果如下：

	"0: Went to USA-Silicon Valley and "
	"1: Went to Britain-London and "
	"2: Went to China-Beijin and "
	"3: Went to France-Paris and "