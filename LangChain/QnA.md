LangChain: Q&A over Documents

An example might be a tool that would allow you to query a product catalog for items of interest.

#pip install --upgrade langchain

import os

​

from dotenv import load_dotenv, find_dotenv

_ = load_dotenv(find_dotenv()) # read local .env file

from langchain.chains import RetrievalQA

from langchain.chat_models import ChatOpenAI

from langchain.document_loaders import CSVLoader

from langchain.vectorstores import DocArrayInMemorySearch

from IPython.display import display, Markdown

file = 'OutdoorClothingCatalog_1000.csv'

loader = CSVLoader(file_path=file)

from langchain.indexes import VectorstoreIndexCreator

#pip install docarray

index = VectorstoreIndexCreator(

    vectorstore_cls=DocArrayInMemorySearch

).from_loaders([loader])

query ="Please list all your shirts with sun protection \

in a table in markdown and summarize each one."

response = index.query(query)

display(Markdown(response))

Name 	Description
Men's Tropical Plaid Short-Sleeve Shirt 	UPF 50+ rated, 100% polyester, wrinkle-resistant, front and back cape venting, two front bellows pockets
Men's Plaid Tropic Shirt, Short-Sleeve 	UPF 50+ rated, 52% polyester and 48% nylon, machine washable and dryable, front and back cape venting, two front bellows pockets
Men's TropicVibe Shirt, Short-Sleeve 	UPF 50+ rated, 71% Nylon, 29% Polyester, 100% Polyester knit mesh, machine wash and dry, front and back cape venting, two front bellows pockets
Sun Shield Shirt by 	UPF 50+ rated, 78% nylon, 22% Lycra Xtra Life fiber, handwash, line dry, wicks moisture, fits comfortably over swimsuit, abrasion resistant

All four shirts provide UPF 50+ sun protection, blocking 98% of the sun's harmful rays. The Men's Tropical Plaid Short-Sleeve Shirt is made of 100% polyester and is wrinkle-resistant

loader = CSVLoader(file_path=file)

docs = loader.load()

docs[0]

Document(page_content=": 0\nname: Women's Campside Oxfords\ndescription: This ultracomfortable lace-to-toe Oxford boasts a super-soft canvas, thick cushioning, and quality construction for a broken-in feel from the first time you put them on. \n\nSize & Fit: Order regular shoe size. For half sizes not offered, order up to next whole size. \n\nSpecs: Approx. weight: 1 lb.1 oz. per pair. \n\nConstruction: Soft canvas material for a broken-in feel and look. Comfortable EVA innersole with Cleansport NXT® antimicrobial odor control. Vintage hunt, fish and camping motif on innersole. Moderate arch contour of innersole. EVA foam midsole for cushioning and support. Chain-tread-inspired molded rubber outsole with modified chain-tread pattern. Imported. \n\nQuestions? Please contact us for any inquiries.", metadata={'source': 'OutdoorClothingCatalog_1000.csv', 'row': 0})

from langchain.embeddings import OpenAIEmbeddings

embeddings = OpenAIEmbeddings()

embed = embeddings.embed_query("Hi my name is Harrison")

print(len(embed))

1536

print(embed[:5])

[-0.021913960576057434, 0.006774206645786762, -0.018190348520874977, -0.039148248732089996, -0.014089343138039112]

db = DocArrayInMemorySearch.from_documents(

    docs, 

    embeddings

)

query = "Please suggest a shirt with sunblocking"

docs = db.similarity_search(query)

len(docs)

4

docs[0]

Document(page_content=': 255\nname: Sun Shield Shirt by\ndescription: "Block the sun, not the fun – our high-performance sun shirt is guaranteed to protect from harmful UV rays. \n\nSize & Fit: Slightly Fitted: Softly shapes the body. Falls at hip.\n\nFabric & Care: 78% nylon, 22% Lycra Xtra Life fiber. UPF 50+ rated – the highest rated sun protection possible. Handwash, line dry.\n\nAdditional Features: Wicks moisture for quick-drying comfort. Fits comfortably over your favorite swimsuit. Abrasion resistant for season after season of wear. Imported.\n\nSun Protection That Won\'t Wear Off\nOur high-performance fabric provides SPF 50+ sun protection, blocking 98% of the sun\'s harmful rays. This fabric is recommended by The Skin Cancer Foundation as an effective UV protectant.', metadata={'source': 'OutdoorClothingCatalog_1000.csv', 'row': 255})

retriever = db.as_retriever()

llm = ChatOpenAI(temperature = 0.0)

qdocs = "".join([docs[i].page_content for i in range(len(docs))])

response = llm.call_as_llm(f"{qdocs} Question: Please list all your \

shirts with sun protection in a table in markdown and summarize each one.") 

​

display(Markdown(response))

Name 	Description
Sun Shield Shirt 	High-performance sun shirt with UPF 50+ sun protection, moisture-wicking, and abrasion-resistant fabric. Fits comfortably over swimsuits.
Men's Plaid Tropic Shirt 	Ultracomfortable shirt with UPF 50+ sun protection, wrinkle-free fabric, and front/back cape venting. Made with 52% polyester and 48% nylon.
Men's TropicVibe Shirt 	Men's sun-protection shirt with built-in UPF 50+ and wrinkle-resistant fabric. Features front/back cape venting and two front bellows pockets.
Men's Tropical Plaid Short-Sleeve Shirt 	Lightest hot-weather shirt with UPF 50+ sun protection, relaxed traditional fit, and front/back cape venting. Made with 100% polyester.

All of these shirts provide UPF 50+ sun protection, blocking 98% of the sun's harmful rays. They also have additional features such as moisture-wicking, wrinkle-resistant, and venting for cool breezes. The Sun Shield Shirt is abrasion-resistant and fits comfortably over swimsuits. The Men's Plaid Tropic Shirt is made with a blend of polyester and nylon and is machine washable/dryable. The Men's TropicVibe Shirt is also wrinkle-resistant and has two front bellows pockets. The Men's Tropical Plaid Short-Sleeve Shirt has a relaxed traditional fit and is made with 100% polyester.

qa_stuff = RetrievalQA.from_chain_type(

    llm=llm, 

    chain_type="stuff",  # map_reduce, refine, map_rerank

    retriever=retriever, 

    verbose=True

)

query =  "Please list all your shirts with sun protection in a table \

in markdown and summarize each one."

response = qa_stuff.run(query)



> Entering new RetrievalQA chain...

> Finished chain.

display(Markdown(response))

Shirt ID 	Name 	Description
618 	Men's Tropical Plaid Short-Sleeve Shirt 	Rated UPF 50+ for superior protection from the sun's UV rays. Made of 100% polyester and is wrinkle-resistant. With front and back cape venting that lets in cool breezes and two front bellows pockets.
374 	Men's Plaid Tropic Shirt, Short-Sleeve 	Rated to UPF 50+ and offers sun protection. Made with 52% polyester and 48% nylon, this shirt is machine washable and dryable. Additional features include front and back cape venting, two front bellows pockets.
535 	Men's TropicVibe Shirt, Short-Sleeve 	Built-in UPF 50+ sun-protection shirt with the lightweight feel. Made with 71% Nylon, 29% Polyester. Wrinkle-resistant with front and back cape venting that lets in cool breezes and two front bellows pockets.
255 	Sun Shield Shirt 	High-performance sun shirt that protects from harmful UV rays. Made with 78% nylon, 22% Lycra Xtra Life fiber. Wicks moisture for quick-drying comfort and fits comfortably over your favorite swimsuit.

All of the shirts listed have sun protection with a UPF rating of 50+ and block 98% of the sun's harmful rays. They are all designed to be lightweight and comfortable in hot weather. They also have additional features such as front and back cape venting and two front bellows pockets. The Sun Shield Shirt is specifically designed to be worn over a swimsuit and is abrasion-resistant for season after season of wear.

response = index.query(query, llm=llm)

index = VectorstoreIndexCreator(

    vectorstore_cls=DocArrayInMemorySearch,

    embedding=embeddings,

).from_loaders([loader])