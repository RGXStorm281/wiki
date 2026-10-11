# Writing a Thesis

## What is a thesis?

When you start an academic thesis, your job is to conduct research on a specific topic and report about the results. The more specific your topic is, the easier your job becomes, because you have a clearly defined task description.

The text you hand in at the end is basically the report of your results. It should not be a diary ("Then I did...") but instead a neutral description of the matter ("Concept A resulted in B, which leads to the conclusion C").

## How is a thesis structured?

A thesis differs from an academic paper mostly in its target audience. A paper is targeted at academics in the same field, so apart from a brief introduction it usually expects prior knowledge about the matter. A thesis in contrast should be a self-contained document, that contains all necessary information for someone with basic computer science education to understand.

This leads to the default structure that you often get presented when asking about a thesis:

- **Introduction:** Describe the motivation for your thesis (Why?), the idea/hypothesis behind your thesis (What?), and sometimes a delimitation to explain how your thesis is placed in a broader landscape and how it is focused. Keep the delimitation short though, it is less important than telling what the thesis is actually about.
- **Background:** Explain the basic knowledge needed to understand your topic: Are there standards you are referencing? Are there software patterns or models you are using? Note that you can require some basic knowledge, like the OSI network stack, basic knowledge about computers, etc. But everything where details are relevant for your concept, you should explain those details first. This chapter should only aggregate information from other sources, it should not contain anything original from your work. This chapter can, but does not need to have a lot of different sources. If you explain a standard, feel free to cite the original document a lot of times. Of course, if you aggregate concepts from a lot of different papers, etc., feel free to include as many as you need.
- **Related Work:** This chapter represents your broad research: It is basically a list of papers that are somewhat similar to what you are doing. And most importantly, in this chapter you state why your approach is different, and why the existing research is not applicable to your problem. Honestly, if you read a lot of these sections the entire academic space just seems like a big argument, where everyone is bashing the others for doing insufficient work ;). You don't include a lot of details on every paper, just enough to say "this does not work for problem X because ...". So as a general rule of thumb: If you support an idea and build on it in your context -> explain it in the background chapter. If you found an alternative approach in your research but decided to do it different -> explain why you differ in the related work chapter. Overall, this chapter is usually not very long (2-3 pages) but you can dump a lot of references here.
- **Concept:** In this chapter, you explain your approach. Bring together all the stuff from your background and explain the idea, how you want to solve or approach the problem from the introduction. This chapter is still abstract, so only provide an overview over your ideas and how they work together, and then dive into the details in the next chapter.
- **Implementation:** This chapter provides important details to your concept. This does not mean code snippets, but rather specific technologies, algorithms, etc.
- **Evaluation:** Next to concept, this is arguably the most important chapter: Here you report the results of your approach, and reflect on successful or failed aspects of your concept. Of course everyone likes to present success, but don't be afraid to also report mistakes. The most important thing is that you have a good reason for your concept, it does not have to succeed. Failure of a tempting approach is also valuable information to others. The evaluation is the "scientific" work of your thesis, so explain the _methodology_ how you evaluate, try to _present results_ in a understandable format (graphs, tables, figures, ...) and finally _explain_ these results (very important!). You should show that you understand your matter in depth.
- **Conclusion:** Here you make the final judgement on the idea/hypothesis of your introduction. Summarize what you found out in your thesis, and make suggestions how future research could continue: What aspects should be followed? Where is further investigation needed? What is not worth pursuing further?

Not every thesis checks all of these boxes. If you implement a standard, the background is basically your concept. And your evaluation can look very different based on the specific topic you are researching: Do you have measurements and numbers that you can present? Do you analyze behavior and trace communication? Do you have rendered imagery that you can show? ...

This structure hopefully gives you an idea for what should be part of your thesis, but the weighting of each section can heavily depend on your task.

## How to start a thesis?

The most important thing about starting a thesis is to nail down your task description. _Why_ are you doing what you're doing, _How_ are you going to approach the problem and _What_ are the expected results and the methodology to test for these hypothesis.

This leads to a document called "Exposé". This should contain:

- The motivation for your work.
- The primary idea / basic concept.
- The expected results.
- The methodology to produce these results.
- A time plan how you distribute the work across the coming months of your thesis.

Trust me, getting a detailed task description is incredibly important. I have been burned by my master thesis, where the chair changed it's mind midway through the thesis. Be bold, and demand a very specific goal you are working towards, and discuss it with your professor and supervisor.

## Bibliography

Referring to sources in the bibliography is unfortunately not an exact science. Every professor has their own preferences what a bibliography should look like: Whether its the number of sources, the "quality" of sources, the formatting, ... you best just ask ahead of time what is important to them.

There are however a couple of general tips I can give you:

1. Use a reference management tool to organize your sources. Recommendation: [Zotero](https://www.zotero.org)
    - Keeps references in one place
    - Allows to store PDFs as attachments
    - Allows highlighting in PDFs
    - Handle multi-line copy from PDFs much better than traditional PDF readers
    - Can export to a .bib file that you can directly use in LaTeX
2. Make sure your reference management tool does not alter information: Recommendation for Zotero: [Better BibTeX](https://retorque.re/zotero-better-bibtex/) extension
    - Allows importing files
    - Allows exporting more complete .bib files
    - Has more settings for making sure Zotero does not alter information (settings > advanced > config editor):
        ```
        extensions.zotero.import.createCollection                           false
        extensions.zotero.import.createNewCollection.fromFileOpenHandler	false
        extensions.zotero.translators.better-bibtex.autoPinMigrated	        true
        extensions.zotero.translators.better-bibtex.bibtexURL	            url
        extensions.zotero.translators.better-bibtex.citekeyFold	            false
        extensions.zotero.translators.better-bibtex.citekeyFormat	        auth.lower + shorttitle(3, 3) + year
        extensions.zotero.translators.better-bibtex.citekeyFormatEditing	auth.lower + shorttitle(3, 3) + year
        extensions.zotero.translators.better-bibtex.exportBraceProtection	false
        extensions.zotero.translators.better-bibtex.exportCaseProtection	on
        extensions.zotero.translators.better-bibtex.exportTitlecase	        off
        extensions.zotero.translators.better-bibtex.fillKeyAfter	        1
        extensions.zotero.translators.better-bibtex.importCaseProtection	on
        extensions.zotero.translators.better-bibtex.importSentenceCase	    off
        extensions.zotero.translators.better-bibtex.journalAbbreviation	    auto
        ```
3. When I add a new source, my default chain of operations is as follows:
    1. Try to find the original source:
        - If the paper,... has a DOI, use the DOI target
        - Otherwise google and prefer big publishers (IEEE, ACM, ...) over something like xarchive
    2. First check whether the publisher offers a BibTeX download (search keywords "cite", "bib", "doi", but for example with ACM its just a large quote icon)
    3. If the source has a DOI, make sure that it is contained in the imported BibTeX or transfer it manually (without the url prefix https://doi.org/)
    4. Unfortunately, the publishers have often **wrong** information!! Therefore, make sure to check the following fields manually against the PDF:
        - Title (e.g. capitalization)
        - Authors
        - (Conference/Thesis/..., if exists)
        - Date (Year + optionally month)
    5. Now you should have the important Information correctly documented. However a bib file can contain a LOT of information. So the final step is decluttering. You only need
        - Authors (or Publisher/Organization if e.g. Documentation Page does not name Authors)
        - Title
        - _One of, if exists_: Proceedings Title, Conference Name, Magazine, ... (Preference in order, use abbreviation if available)
        - _If exists_: Page Range, Volume / Version
        - _One of, if exists_: DOI, URL, ISBN (Preference in order)
        - _For web pages_: Visit date

## Catalogs for Literature Research

Here are some sources where you could find relevant literature in your research (the most yielding ones from my experience are highlighted in bold):

- Proprietary Catalogs
    - [**ACM Digital Library**](https://dl.acm.org)
    - [**IEEE Explore**](https://ieeexplore.ieee.org/Xplore/home.jsp)
    - [ScienceDirect](https://www.sciencedirect.com/)
    - [Springer Link](https://link.springer.com)
    - Statista
    - [**Uni Bib-Katalog**](https://rds-tue.ibs-bw.de/opac/)
- Meta Catalogs
    - BSZ (Stuttgart-Tübingen)
    - K10plus (10 Bundesländer)
- Meta Search Engines
    - [**google Scholar**](https://scholar.google.com)
    - Bielefeld Academic Search Engine (BASE)
    - [**KVK (Karlsruher Virtueller Katalog)**](https://kvk.bibliothek.kit.edu/)
