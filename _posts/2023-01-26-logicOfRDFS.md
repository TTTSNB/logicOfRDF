---
title: "logicOf_RDFS"
date: 2023-01-26
---


logicOf RDFS
--

RDFS is the schema language of the Resource Description Framework (RDF). In general, a schema language, for some information management framework, m, provides m’s users with information concerning how m may be used to describe data. In the case of RDFS, this is achieved by demonstrating RDF constructs may be used to describe additional constructs with greater semantic power. As you should know, RDF is, essentially, about graphs. That is a set of RDF triples is really just a graph. RDFS, in contrast, is about sets. Or, to be more precise, it is about some quasi-set theoretic constructs that may understood in terms of pre-existing RDF constructs and used to better model the meaning of data. 


The constructs used in RDFS descriptions are: 

    1. Classes: a construct for describing types of objects
    
    2. subClasses: a construct for relating types of objects
    
    3. Properties: a construct for describing objects
    
    4. subProperties: a construct for relating the description of objects.

The semantics of RDFS (i.e., how it is able to talk about quasi-sets/Classes) is given via the logical mechanism of inference. Meaning that the significance of any construct in RDFS (e.g., Classes, subClasses, Properties, subProperties) is determined, in full, by what may be inferred on its basis. RDFS’s class system, as a whole, rests upon a the notion of inheritance, which is based loosely on the notion of set inclusion. The significance of inheritance may be understood to take the form of the Type Propagation pattern of inference, which states: if some class, C, is a subClass of some Class, C’, then all instances of C are instances of C’. In use, this pattern of inference may be used to construct a brief and simple definition for the construct: rdfs:subClass. The relationship between the Property Propagation pattern of inference and the rdfs:subPropertyOf construct works in much the same way. In addition to the subClass and subProperty constructs, RDFS includes a set of constructs for describing the relationships that may hold between Classes and Properties. These are the properties of rdfs:domain, which indicates the type of Class some property takes as its subject, and rdfs:range, which indicates the type of Class some property takes as its object. Indications of the domain and range of some property allows users to infer the Class of a resource that shows up as the subject or object, respectively of that property. 

In what follows, I review the inference rules of RDFS and then turn to outlining a few modeling patterns that may be used to formulate certain important schemata in first-order logic, which may be used to approximate important notions in set theory. 

1.THE INFERENCE RULES OF RDFS
--
INFERENCE RULE: rdfs:subClassOf
AKA: Type propagation rule
ASSUMED STANDARDS: RDF
DEFINITION
CONSTRUCT	{?r rdf:type ?B	} 
WHERE			
          {			
            ?A rdfs:subClassOf ?B	. 
            ?r rdf:type ?A	.	
          }			
          


INFERENCE RULE:				rdfs:subPropertyOf
AKA:							Property propagation rule
ASSUMED STANDARDS:		RDF
DEFINITION:
								CONSTRUCT	{	?x		?r 						?y	.	} 	
								WHERE			{		
													?x		?q 						?y	.		
													?q		rdfs:subPropertyOf		?r	. 	
												}			


INFERENCE RULE:				rdfs:domain
AKA:							Domain inference rule
ASSUMED STANDARDS:		RDF
DEFINITION:
								CONSTRUCT	{	?x		rdf:type					?D	.	} 
								WHERE			{		
													?P		rdfs:domain				?D	.	
													?x		?P						?y	. 	
												}			

GENERALIZATION:
								CONSTRUCT	{	?P		rdfs:domain				?C	.	} 
								WHERE			{		
													?P		rdfs:domain				?D	.	
													?D		rdfs:subClassOf			?C	. 	
												}			


INFERENCE RULE:				rdfs:range
AKA:							Range inference rule
ASSUMED STANDARDS:		RDF
DEFINITION:
								CONSTRUCT	{	?y		rdf:type					?R	.	} 
								WHERE			{		
													?P		rdfs:range				?R	.	
													?x		?R						?y	. 	
												}			

GENERALIZATION:
								CONSTRUCT	{	?P		rdfs:range				?C	.	} 
								WHERE			{		
													?P		rdfs:range				?D	.	
													?D		rdfs:subClassOf			?C	. 	
												}			

BONUS:
INFERENCE RULE:				Generalized propagation rule for some type, T
ADDITIONAL STANDARDS:		RDF, SHACL

FORM:
								:T	sh:rule	“CONSTRUCT	{	$this		rdf:type 			?B	} 
											WHERE			{
																?A			rdfs:subClassOf		?B	. 
																$this		rdf:type				?A	.	
															}”	.	
LEDGEND:
							$this = a variable used to reference any member of the attached class (in this case, class T).
							“?B” = superclass variable
							“?A” = middle term variable 



2.RDFS MODELING: COMBINATIONS AND PATTERNS
--

Set Intersection (in rdfs)
--

TARGET INFERENCE: 		If resource x is rdf:type :C, then x is rdf:type :A and :B as well.
FORMAL EXPRESSION:		(∀x) (Cx ⥰ Ax ● Bx)
PATTERN EXPRESSION:		Make class, C, a common rdfs:subClassOf both A and B.
INFERENTIAL SUPPORT:	Type propagation rule
INFERENTIAL LIMIT:			Does not express: (∀x) (Ax ● Bx ⥰ Cx); thus, only approximates true set intersection.
INFERENCE IN TRIPLES:					
							IF		:C 	rdfs:subClassOf 	:A	;
									:C 	rdfs:subClassOf 	:B	;
									?x 	rdf:type 			?C	.
							THEN —————————————
									?r	rdf:type			:B	.
									?r	rdf:type			:A	.	


Set Union (in rdfs)
--

TARGET INFERENCE: 		If resource x is rdf:type :A or :B, then x is rdf:type C as well.
FORMAL EXPRESSION:		(∀x) (Ax ⋁ Bx ⥰ Cx)
PATTERN EXPRESSION:		Make A and B rdfs:subClassOf C.
INFERENTIAL SUPPORT:	Type propagation rule.
INFERENTIAL LIMIT:			Does not express: (∀x) (Cx ⥰ Ax ⋁ Bx); thus, only approximates true set union.
INFERENCE IN TRIPLES:					
							IF		:A	rdfs:subClassOf 	:C	;
									:B 	rdfs:subClassOf 	:C	;
									?x 	rdf:type 			?A	.	(OR: ?x 	rdf:type ?A	.)
									
							THEN —————————————
									?x	rdf:type			:C	.	


Set Equivalence 
--

TARGET INFERENCE: 		Class :A is equivalent to class :B.
FORMAL EXPRESSION:		(∀x) (Ax ≡ Bx)
PATTERN EXPRESSION:		Make class :A and :B rdfs:subClassOf of each other.
INFERENTIAL SUPPORT:	Type propagation rule.
INFERENCE IN TRIPLES:					
							IF		:A	rdfs:subClassOf 	:B	;
									:B 	rdfs:subClassOf 	:A	;
									?x 	rdf:type 			:A	.	(OR: ?y 	rdf:type :B	.)
									
							THEN —————————————
									?x	rdf:type				:B	. 	(OR: ?y 	rdf:type :A	.)


Property Intersection (in rdfs)
--

TARGET INFERENCE: 		If resources x and y are related by property :P, then they are by properties R and S in the same way.
FORMAL EXPRESSION:		(∀x)(∀y)(Pxy ⥰ Rxy ● Sxy)
PATTERN EXPRESSION:		Make both properties R and S rdfs:subProperyOf P.
INFERENTIAL SUPPORT:	Property propagation rule.
INFERENTIAL LIMIT:			Does not express: (∀x)(∀y)(Rxy ● Sxy ⥰ Pxy); thus, only approximates true property intersection.
INFERENCE IN TRIPLES:					
IF --
:P rdfs:subPropertyOf :R	;
:P 	rdfs:subPropertyOf 	:S	;
									?x 	:P 						?y	.
THEN-- 
									?x	:R						?y	.
									?x	:S						?y	.	


Property Union (in rdfs)
--

TARGET INFERENCE: 		If resources x and y are related by property :P or :Q, then those resources are related to property :R in the same way.
FORMAL EXPRESSION:		(∀x)(∀y)(Pxy ⋁ Qxy ⥰ Rxy)
PATTERN EXPRESSION:		Make properties :P and :Q  rdfs:subClassOf  :R.
INFERENTIAL SUPPORT:	Property propagation rule.
INFERENTIAL LIMIT:			Does not express: (∀x)(∀y)(Rxy ⥰ Pxy ⋁ Qxy); thus, only approximates true property union.
INFERENCE IN TRIPLES:					
							IF		:P	rdfs:subClassOf 	:R	;
									:Q 	rdfs:subClassOf 	:R	;
									?x 	:P 					?y	.	(OR: ?x 	:P ?y	.)
									
							THEN 
									?x	:R					:C	.

Property Equivalence
--

TARGET INFERENCE: 		Property :P is equivalent to property :Q
FORMAL EXPRESSION:		(∀x)(∀y)(Pxy ≡ Qxy)
PATTERN EXPRESSION:		Make properties :P and :Q rdfs:subPropertiesOf the other.
INFERENTIAL SUPPORT:	Property propagation rule.
INFERENCE IN TRIPLES:					
							IF		:P	rdfs:subPropertyOf 	:Q	;
									:Q 	rdfs:subPropertyOf 	:P	;
									?x 	:P						?y	.	(OR: ?x :Q ?y	.)
									
							THEN
									?x	:Q						?y	. 	(OR: ?x :P ?y	.)


Property Transfer
--

TARGET INFERENCE: 		Resources related by property :A in source 1 are related in the same way by property :B in source 2.
FORMAL EXPRESSION:		(∀x)(∀y)(Axy ⥰ Bxy)
PATTERN EXPRESSION:		Make property :A rdfs:subPropertyOf of :B.
INFERENTIAL SUPPORT:	Property propagation rule.
INFERENCE IN TRIPLES:			
							IF		?x	:A 	?y	.
							THEN 
									?x	:B	?y	.

Source:
--
Semantic Web for the Working Ontologist, ch.8

